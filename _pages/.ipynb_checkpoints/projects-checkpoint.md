---
layout: single
title: "Projects"
permalink: /projects/
author_profile: true
classes: compact-text
---

A collection of Machine Learning and Data Science projects. Each write-up covers the problem, my approach, and key results — click through to the GitHub repo for full code and details.

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
