---
layout: default
title: Home
---

<h1>{{ "Hello World!" | downcase }}</h1>

<h1> Category List </h1>

{% for category in site.categories %}
<h3>{{ category[0] }}</h3>
<ul>
  {% for post in category[1] %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
{% endfor %}
  

