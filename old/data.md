---
layout: page
title:  Open Data
permalink: /data/
---

<div class="home">

  <ul class="post-list">
    {% for post in site.posts %}
      {% if post.categories contains 'data' %}

        <li>  
          <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
          <h1>
            <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
            <h4><p>{{ post.excerpt }}</p></h4>

          </h1>
        </li>
      {% endif %}      
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

</div>