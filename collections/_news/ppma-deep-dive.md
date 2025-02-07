---
title: "Privacy-preserving model auditing demo: technical deep dive"
publish_date: 2025-02-07
permalink: /blog/ppma-deep-dive/
img_alt_text: Example Cryptographic Protocol
image: /assets/img/news/privacy-preserving-model-auditing-demo-technical-deep-dive.jpg
image_accessibility: Example Cryptographic Protocol
layout: blog
post_author: Tomo Lazovich
---

In my last blog post, I gave an overview of how we used an existing open source cryptographic protocol for the use case of measuring demographic disparities in a model's performance. If you haven't read that one, you can find it [here]({{ "/blog/privacy-preserving-model-auditing" | relative_url }}). In this post, we'll dive more into some of the engineering that it took to get the demo working on GSA's [cloud.gov](https://cloud.gov/) infrastructure. If you're interested in looking at the demo code, you can find it on [Github](https://github.com/XDgov/privacy-preserving-model-auditing-demo). 

## Introduction: what were the roadblocks?

As a reminder, for this demo, we decided to use the open source [Private-ID](https://github.com/facebookresearch/Private-ID) library, a Rust library that implements several private join protocols. After getting the package running locally in my OS X development environment using their sample data, my next step was to try to get it working in the cloud. The GSA cloud.gov infrastructure is based on [CloudFoundry](https://www.cloudfoundry.org/), so you'll notice in the repository that all the deployment scripts used CloudFoundry commands like `cf push`. At a very basic level, we're trying to deploy one end of the computation to the cloud and run the other end of the computation locally and have those two ends communicate with one another. 

In this post, I'll focus on three main issues that I had to figure out to get this demo running:

- **Cross-compilation**: My local development environment is OS X, but the cloud servers run Linux. So, I first needed to figure out how to cross-compile a Rust binary for a Linux target on my Macbook. 

- **gRPC -> REST API**: After some wrangling with support, I discovered that cloud.gov's load balancers do not support HTTP2, meaning that the default gRPC communications between the server and client wouldn't work. Therefore, I needed to figure out how to wrap the library's gRPC endpoints in a REST API. This involved two steps: 1) creating a proxy server to translate REST API requests to gRPC, and 2) modifying the client-side code in the Rust library to send REST API requests instead of gRPC. 

- **Networking configuration**: The final step was figuring out how to configure network routes with CloudFoundry so that the proxy server and the gRPC server had permission to communicate with each other. 

I'll go into each of these in more detail below.

## Cross-compiling Rust binaries from OS X to Linux

The first issue I spent some time wrangling with was how to get the Rust binary built for the cloud Linux target. If you know anything about CloudFoundry, you know that it is based around [buildpacks](https://docs.cloudfoundry.org/buildpacks/system-buildpacks.html), which support specific languages and frameworks and allow you to configure the build of your binary as part of deployment to the cloud. You might immediately wonder why we needed to cross-compile at all - why couldn't we just use a buildpack? First off, Rust doesn't have a buildpack that is officially supported by CloudFoundry. Thankfully, there is an open source Rust buildpack on [Github](https://github.com/alphagov/cf-buildpack-rust). I first tried deploying with this buildpack rather than cross-compiling locally. In principle, this solution would work, but in my case the Rust build process was very memory-intensive and we were working in a sandbox environment with limited RAM available, so my builds would often get about halfway through and fail due to running out of memory. Because of this constraint, I ended up deciding to build locally and deploy the binary to the cloud using CloudFoundry's [binary buildpack](https://docs.cloudfoundry.org/buildpacks/binary/index.html).

Once I decided that I needed to build locally, I then turned to the question of how to cross-compile Rust from OS X to Linux. After experimenting with a few options, the one that I found easiest to use was a Rust compiler called [Cross](https://github.com/cross-rs/cross). Cross works by fetching a docker container for the environment you need to compile into and running the compilation within that container. Once Cross is installed, the cross-compilation is a simple one-liner:

```bash
cross build --release --target x86_64-unknown-linux-gnu
```

This command puts the binary in the directory `target/x86_64-unknown-linux-gnu/release/`.

After this, you now have a Linux binary that can be pushed to the cloud for the server (or "company") side of the cryptographic protocol. 

## Building a REST API around the gRPC endpoints

My next step was to wrap the gRPC endpoints of the server in a REST API because the client (or "partner") side was not able to communicate via HTTP2 with the server because cloud.gov's load balancers did not support the protocol. Note, however, that HTTP2 traffic was supported _internally_, meaning that what I needed was a proxy server that would receive commands from the client via a REST API and translate those requests to gRPC to send to the RPC server that actually runs the protocol. Now, this does mean that we are essentially giving up all the advantages that come with gRPC, particularly as it relates to data streaming, but the purpose of this demo was really to show what was possible rather than building the optimal system.

Luckily, there is a wonderful tool called [grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) that is built for exactly this purpose. This package takes in the proto files that define the gRPC procedures and generates stubs in Go that do the translation from the REST API to the appropriate gRPC call. All you need to do is annotate your proto file correctly, as you can see in [this example](https://github.com/XDgov/privacy-preserving-model-auditing-demo/blob/main/pjc-proxy-go/proto/pjc-proxy/pjc.proto#L62) in our repo. After that, the last step is to create a main Go file that acts as the proxy server and calls the appropriate generated code like the one we have [here](https://github.com/XDgov/privacy-preserving-model-auditing-demo/blob/main/pjc-proxy-go/main.go). For more details on how do actually run the generation using a tool called `buf`, you can see the repo's [documentation](https://github.com/XDgov/privacy-preserving-model-auditing-demo?tab=readme-ov-file#generate-go-proxy-server-code-and-cross-compile-proxy-binary). I find a figure from `grpc-gateway`'s documentation to be particularly illustrative of how the proxying works:

![proxy configuration]({{ site.baseurl }}/assets/img/news/privacy-preserving-model-auditing-demo-technical-deep-dive.jpg)

The Go proxy is deployed as a separate app on cloud.gov to talk to the RPC server app that we compiled above. For this deployment we also build the Go server locally (thankfully the Go compiler supports cross-compilation) and use the binary buildpack as we did with the RPC server. 

## Updating Rust client to query REST API

With the gRPC server now wrapped in a REST API, we turn the client or "partner" side of the computation. In the original library, the client Rust code makes gRPC calls to the server. With our server now wrapped in a REST API, we need our client to format the data and make appropriate calls to that API. This involves first making sure that the data, originally transferred in protobufs, are correctly serialized as JSON to send to the new API. To do this, we add prefixes to some buffer definitions to serialize the data as base64, which is what the Go proxy expects (and also what it sends back when you query for results from the API). You can see how that is done [here](https://github.com/XDgov/privacy-preserving-model-auditing-demo/commit/14719b4f50b502b9a4f0408ef144c53d926bed7d#diff-63cdbbd02ee60550190dfcc179aa3e7af8abc22f5598a87e1c32d1c33a0795eaR16) and [here](https://github.com/XDgov/privacy-preserving-model-auditing-demo/commit/14719b4f50b502b9a4f0408ef144c53d926bed7d#diff-6eb41e27fe9db4f78129034b9f3df8969db643f73e8f2561e386495fcdaecd94R14), for example. 

Once serialization is taken care of, the client itself needs to actually call the correct API endpoints. To do this, we update the code to create an HTTP client with the `reqwest` library, serialize the data as JSON, and then send the HTTP requests to the appropriate API endpoint. You can see how that is done in the diff [here](https://github.com/XDgov/privacy-preserving-model-auditing-demo/commit/14719b4f50b502b9a4f0408ef144c53d926bed7d#diff-4cc788698e80640ce0458d1d1ceeef8920b47f2094eacec6a73c50c52b298117R139). Here is also a small code snippet that shows a request to the REST API, this one asking for the results of the computation from the server:

```rust
let proto_stats = http_client.post(
        format!("{}/v1/recv_stats", &host_pre.unwrap())
    ).send().await?.json::<Stats>().await?;
```

Compare that to what the same code looked like using gRPC instead:

```rust
let proto_stats = rpc_client::recv_stats(&mut client_context)
        .await?
        .into_inner();
```

From a code simplicity standpoint, the REST API requests do add a little bit of cruft to the code compared to the gRPC calls, but overall it's not a huge change. Ultimately you might actually be able to write a code generation routine that would do this kind of translation automatically, but you would still need to tend to details like the correct serialization format for the various buffers defined in the code. 

## Configuring the networking on cloud.gov

Now that our client is ready to communicate with the proxy server on cloud.gov, our last step is ensuring that the proxy server actually has the permission to communicate with the RPC server that is running the computation. While this was not the trickiest part of the process, it did require understanding a bit about how CloudFoundry networking permissions work. In the end, two commands are needed to get the job done.

First, we need to allow the proxy server application to send traffic to the internal RPC server:

```bash
cf add-network-policy $PROXY_PREFIX $RPC_PREFIX -s dev -o census-xd-pets-prototyping --protocol tcp --port 8080
```

Then, we need to allow our RPC server to receive internal traffic via HTTP2:

```bash
cf map-route $RPC_PREFIX apps.internal --hostname $RPC_PREFIX --app-protocol http2
```

That should do it! It might seem a bit silly to have gone through all this work to just get around the fact that we could not communicate externally via HTTP2 even though we could internally, but I still think it's a useful and illustrative example of how to make your gRPC-based app work in different environments.

That's all for this post. In the next post, I will go into more detail on how the final model auditing demo actually works. 