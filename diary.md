---
layout: page
title: Diary
subtitle: Even if yoU’re on the right traCK, you’ll get Run over if you just sit there  —Will Rogers
---

<div>
{% assign postsCategory = site.posts | group_by_exp:"post", "post.categories"  %}
{% for category in postsCategory %}

{% if category.name[0] != "Diary" %}
	{% continue %}
{% endif %}

<h4 class="post-teaser__month">
<strong>
{% if category.name %}
- - - - - {{ category.name }} - - - - - 
{% else %} 
{{ Print }} 
{% endif %}
</strong>
</h4>
<ul class="list-posts">
{% for post in category.items %}
<li class="post-teaser">
<a href="{{ post.url | prepend: site.baseurl }}">
<span class="post-teaser__title">{{ post.title }}</span>
<span class="post-teaser__date">{{ post.date | date: "%d %B %Y" }}</span>
</a>
</li>
{% endfor %}
</ul>
{% endfor %}
</div>