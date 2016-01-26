---
layout: page
title: Archive
image: /assets/front_matter_image.jpg
---

## Blog Posts

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})  <br> ![image]({{ post.image }})
{% endfor %}
