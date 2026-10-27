---
layout: single
title: "Projects"
permalink: /projects/
author_profile: true
classes: compact-text
---

This is my collection of Machine Learning and Data Science projects. You will notice how varied the project topics are. This is by design to show my ability to gain subject-matter expertise in different spaces/client interests. My math research involved learning disparate mathematical fields in order to apply techniques in those fields to my problems. I can use this ability gained from over 5 years of complex research to pick up a breadth of skills and subject matter. With my background and the analytical ability unique to mathematicians, I would require a shorter ramp-up, with higher potential, over someone with work experience alone. Invest in your longterm future with me. 

Each write-up covers the problem, my approach, and key results. Click through to the GitHub repo for full code. 
Experience alone might be a head start, but I quickly catch-up. 
<div class="entries-list">

{% for project in site.projects %}
  <div class="list__item">
    <article class="archive__item">
      <h2 class="archive__item-title"><a href="{{ project.url }}">{{ project.title }}</a></h2>
      <p class="archive__item-excerpt">{{ project.excerpt }}</p>
    </article>
  </div>
{% endfor %}

</div>
