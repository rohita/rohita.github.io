---
layout: default
title:  A Programmer's Blog
---

<div>
  {% for post in site.posts %}
    <div>	
      <small>{{ post.date | date: "%b %-d" | upcase }}</small>&emsp;<a href="{{ post.url }}">{{ post.title }}</a>
      &emsp;<span class="tag">{{ post.tags | join: ", " | upcase }}</span>
    </div>
  {% endfor %}
  <p></p>
</div>
