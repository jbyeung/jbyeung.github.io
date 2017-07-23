---
layout: food-home
title: Food Recipes
---

<div class="posts clearfix">

  {% for food in site.food %}
    <article class="post">    
    <a href="{{ food.url }}">
      {% if food.image %}
      <img src="../assets/food/{{ food.image }}" class="thumbnail">
      {% endif %}
    <center>{{ photo.title }}</center>
    </a>
    </article>
  {% endfor %}
</div>
