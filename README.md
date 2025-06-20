# xD.gov

## About

The xD site is a static site built with [Jekyll](https://jekyllrb.com/).

## Install dependencies

Install Ruby v3 or greater: [https://www.ruby-lang.org/en/documentation/installation/](https://www.ruby-lang.org/en/documentation/installation/)

Once Ruby is installed, you can install dependencies contained in the Gemfile via:

```bash
bundle install
```

Bundler is a package manager that comes preinstalled with modern versions of Ruby. You can read more about Bundler here: [https://bundler.io/](https://bundler.io/).

## Running Code Locally

After cloning the repo, from the project root run:

```bash
jekyll serve
```

If you have errors with the above command, you may need to run Jekyll in a bundler context like so:

```bash
bundle exec jekyll serve
```

## Builds and Deployments

The primary branch of this repository is `master`, however the deployment branch is `gh-pages`. To ensure any updates are deployed to xd.gov, ensure you merge changes to `master` and then to `gh-pages`, which should be automatically detected and deployed by the Pages Action.

Additionally, opening a new pull-request will trigger a preview-build action to ensure your changes render as expected.

## Updating Ruby

If you need to update the version of Ruby, be sure to specify the version both in the `Gemfile` and `.ruby-version` file. The later is used by Cloud.gov: [https://cloud.gov/pages/documentation/rvm-on-pages/](https://cloud.gov/pages/documentation/rvm-on-pages/).

## Embedding Videos

Currently we can embed YouTube videos (see the [2024 PEPR presentation post](collections/_news/pepr-2024-presentation.md) for an example). If you want to add videos from other sources see this repository for examples: https://github.com/nathancy/jekyll-embed-video. We include responsive styling suggested in this repo in `_news-item.scss`.

## Code Syntax Highlighting

Syntax highlighting is handled by Kramdown and Rouge.
[Jekyll Documentation](https://jekyllrb.com/docs/liquid/tags/#code-snippet-highlighting)
To update the syntax styling, add create a new style from these [examples](https://jwarby.github.io/jekyll-pygments-themes/languages/ruby.html), add it to the folder `assets/css/_syntax_highlighting`, and import it in `assets/css/main.scss`.

## JavaScript compression

Using the tools [Browserify](https://browserify.org/) and [UglifyJS](https://github.com/mishoo/UglifyJS), JavaScript used in the application can be transformed into a form appropriate for running in a browser (via Browserify) and minified (via UglifyJS) to reduce the bundle size sent to a user's browser.

## Analytics

The file `_includes/head.html` contains script tags for the following analytics tools:
[Digital Analytics Program](https://digital.gov/guides/dap/)
[Google Analytics](https://marketingplatform.google.com/about/analytics/)
