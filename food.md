---
layout: food-home
title: Food Recipes
---

<div class="posts clearfix">

  {% for food in site.food %}
    <article class="post">    
    {% if food.image %}
    <a href="{{ food.url }}"><img src="../assets/food/{{ food.image }}" class="thumbnail">
    </a>
    {% endif %}

    <center>{{ food.title }}</center>
    </article>
  {% endfor %}
</div>
