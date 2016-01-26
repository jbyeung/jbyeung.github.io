---
layout: projects-home
title: Projects
---


<div class="posts clearfix">
  {% for project in site.projects %}
    <article class="post">    
      <h1><a href="{{ project.url }}">{{ project.title }}</a></h1>

      <div class="entry">
        <p>{{ project.description }}</p>
        {{ project.content | truncatewords:40}}
  </div>
      
      <!-- <a href="{{ post.proj-url }}" class="small-link primary-link" target="_blank">View Project Site <i class="fa fa-external-link"></i></a> -->
      <a href="{{ site.baseurl }}{{ post.url }}" class="small-link">Read more</a>
    </article>
  {% endfor %}
</div>