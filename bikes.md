---
layout: page
title:  Bicycle Touring
permalink: /bikes/
---

<div class="home">

  <ul class="post-list">
    {% for post in site.posts %}
      {% if post.categories contains 'biking' %}

        <li>  
          <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
          <h2>
            <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
            <p>{{ post.excerpt }}</p>
          </h2>
        </li>
      {% endif %}      
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

</div>