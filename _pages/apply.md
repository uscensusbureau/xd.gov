---
permalink: /apply/
layout: page
title: Apply
seo_excerpt:
  We’re looking for purpose-driven technologists and innovators to be part
  of our growing emerging technologies team.
---

<section class="apply-overview">
    <div class="grid-container">
        <div class="section-breadcrumb">Apply to xD</div>
        <h1>Emerging Technology Fellowship</h1>
        <p>
          We’re looking for purpose-driven technologists and innovators to 
          join this unique fellowship experience with xD. The <b>Emerging 
          Technology Fellowship (ETF)</b> recruits the best and brightest
          technologists with expertise in emerging data technologies to
          build a better government for everyone.
        </p>
        <div class="usa-alert usa-alert--info">
            <div class="usa-alert__body">
                <h4 class="usa-alert__heading">Applications Now Open</h4>
                <p class="usa-alert__text">
                    Applications are now being accepted on a rolling basis until 
                    filled through <strong>January 15, 2023</strong>. See details below.
                </p>
            </div>
        </div>
        {% for position in site.positions %} 
            {% include components/position.html position=position %}
        {% endfor %}
        <!--<div class="grid-row">
            <div class="grid-col-12">
                <h3>Our Next Cohort</h3>
                <p>
                    <strong>
                        Applications for the Spring 2022 Cohort of the ETF are
                        now closed.
                    </strong>
                    Future openings will be listed on this page when we have
                    more information to share.
                </p>
            </div>
        </div>-->
    </div>
</section>

<section class="apply-overview">
  <div class="grid-container">
    <div class="grid-row">
      <div class="section-breadcrumb">Why Should You Apply To The ETF?</div>
    </div>
    <div class="grid-row">
      <p>
        Fellows are driven by a sense of curiosity and bring their know-how and intellect 
        to projects while viewing challenges as a blank slate to introduce ground-breaking 
        technical solutions. Enthusiasm to share their ideas, make a positive impact, and 
        to guide others in the pursuit of the high-tech modernization of government is a 
        driver for these leaders. Being unafraid to make waves when necessary while 
        maintaining a healthy approach to risk mitigation, fellows bring an 
        entrepreneurial spirit to their work and believe in their ability to bring lasting 
        change as part of a dedicated team.
      </p>
    </div>
    {% 
      include components/praise.html 
      content="Working at xD gives an opportunity to work on interesting data problems with interesting people from within one of the most quietly important agencies in the U.S. There is no shortage of issues in government that can be improved with better collection, management, and use of data, and xD gives exposure to a bunch of them." 
      author="Aidan Feldman" 
      author_title="2017 Fellow. Technologist, Dancer, Adjunct Assistant Professor of Public Service NYU|Wagner" 
      image_path="/assets/img/praise/feldman.jpeg" 
    %}
    <div class="grid-row">
      <div class="section-breadcrumb">What Skills Are We Looking For?</div>
    </div>
    <div class="grid-row">
      <p>
        We’re seeking candidates with professional experiences in the following
        categories:
      </p>
    </div>
    <div class="grid-row grid-gap">
      <div class="tablet:grid-col">
        <ul class="usa-icon-list usa-icon-list--primary">
          {% include components/icon-list-item.html content="Data Science" %}
          {% include components/icon-list-item.html content="Machine Learning" %}
          {% include components/icon-list-item.html content="Natural Language Processing" %}
          {% include components/icon-list-item.html content="Responsible AI " %}
        </ul>
      </div>
      <div class="tablet:grid-col">
        <ul class="usa-icon-list usa-icon-list--primary">
          {% include components/icon-list-item.html content="Data Governance & AI Policy" %}
          {% include components/icon-list-item.html content="Privacy-enhancing Technologies" %}
          {% include components/icon-list-item.html content="Algorithmic Bias" %}
        </ul>
      </div>
    </div>
  </div>
</section>

<section class="apply-overview apply-faq">
    <div class="grid-container">
        <div class="section-breadcrumb">Frequently Asked Questions</div>
        <div class="grid-row">
            <div class="grid-col-12">
                <br/>
                {% for faq in site.data.faqs %}
                    {% include components/faq.html faq=faq %}
                {% endfor %}
            </div>
        </div>
    </div>
</section>
