---
layout: food-home
title: Food
---

<div class="posts clearfix">

  {% for food in site.food %}
    <article class="post">    
      <h3><a href="{{ food.url }}">{{ food.title }}</a></h3>

      <div class="entry">
        <p>{{ food.description }}</p>
      </div>

      <a href="{{ food.url }}" class="small-link primary-link">View Post</a>

    </article>
  {% endfor %}
</div>
