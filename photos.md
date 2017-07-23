---
layout: photos-home
title: Photos
---


<div class="posts clearfix">

  {% for photo in site.photos %}
    <article class="post">    
    {% if photo.image %}
    <a href="{{ photo.url }}"><img src="../assets/photos/{{ photo.image }}" class="thumbnail">
    </a>
    <!--<center>{{ photo.title }}</center>-->
      
    {% endif %}
    </article>
  {% endfor %}
</div>
