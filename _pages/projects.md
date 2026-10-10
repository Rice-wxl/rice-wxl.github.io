---
layout: page
title: projects
permalink: /projects/
description: Selected research projects from my undergraduate years at Brown and earlier PhD work.
nav: true
nav_order: 2
---

<!-- pages/projects.md -->
<div class="project-list">
  {% assign sorted_projects = site.projects | sort: "importance" %}
  {% for project in sorted_projects %}
    <div class="project-row">
      <a class="project-thumb" href="{{ project.url | relative_url }}" aria-label="{{ project.title }}">
        {% include figure.liquid loading="lazy" path=project.img sizes="(min-width: 768px) 220px, 95vw" alt=project.title %}
      </a>
      <div class="project-body">
        <h3 class="project-title">
          <a href="{{ project.url | relative_url }}">{{ project.title }}</a>
          {%- if project.affiliation %}<span class="project-affiliation">{{ project.affiliation }}</span>{% endif %}
        </h3>
        <div class="project-topics">
          {% for topic in project.topics %}<span>{{ topic }}</span>{% endfor %}
        </div>
        <div class="project-links">
          <a href="{{ project.url | relative_url }}">Details <i class="fa-solid fa-arrow-right"></i></a>
          {% for link in project.links %}
            <a href="{% if link.url contains '://' %}{{ link.url }}{% else %}{{ link.url | relative_url }}{% endif %}">
              {%- if link.name == 'Code' -%}<i class="fa-brands fa-github"></i>{%- elsif link.url contains '.pdf' -%}<i class="fa-regular fa-file-pdf"></i>{%- else -%}<i class="fa-solid fa-arrow-up-right-from-square"></i>{%- endif %} {{ link.name -}}
            </a>
          {% endfor %}
        </div>
      </div>
    </div>
  {% endfor %}
</div>
