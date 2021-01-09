---
layout: default
title:  A Programmer's Blog
---

<div>
  {% for post in site.posts %}
	<div  class="post-title">
		<h1><a href="{{ post.url }}">{{ post.title | default:page.title }}</a></h1>
		{% include post_title.html %}
	</div>
    <div class="post-content">
    	{{ post.excerpt }} 
    </div>
    <div  class="read-more">
    	<a href="{{ post.url }}">READ MORE</a>
	</div>
  {% endfor %}
  <p></p>
</div>
