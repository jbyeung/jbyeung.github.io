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
    <h3><center>{{ food.title }}</center></h3>
    </a>

    <div class="entry">
      <p>{{ food.description }}</p>
    </div>

    <a href="{{ food.url }}" class="small-link primary-link">Read More</a>

    </article>
  {% endfor %}
</div>
