---
# permalink: /team/
image: /assets/img/pages/index/xd-og.png
layout: page
title: xD Team
seo_excerpt: meet the members of the xD team
---
<div class="page-bios">
  <div class="grid-container">
    <div class="grid-row">
      <div class="margin-bottom-3">
        <section class="mission">
          <div class="breadcrumb">Our Team</div>
          <div>The xD team represents an array of disciplines and skillsets. We're a remote-first organization spanning the United States.</div>
        </section>
      </div>
    </div>
  </div>

  <section class="bios-content">
    <div class="grid-container">
      <div class="breadcrumb">Meet the Team</div>
      <div class="grid-row grid-gap-6">
        {% assign team_members = site.team_members %}
        {% for member in team_members %}
          {% include components/team-member-card.html member=member %}
        {% endfor %}
      </div>
    </div>
  </section>
</div>
