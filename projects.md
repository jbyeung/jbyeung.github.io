---
layout: projects-home
title: Projects
---

<div class="posts clearfix">

  {% for project in site.projects %}
    <article class="post">    
      <h3><a href="{{ project.url }}">{{ project.title }}</a></h3>

      <div class="entry">
        <p>{{ project.description }}</p>
      </div>

      <a href="{{ project.url }}" class="small-link primary-link">View Project Site</a>

    </article>
  {% endfor %}
</div>
