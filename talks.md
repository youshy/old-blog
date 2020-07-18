---
layout: page
title: Talks
permalink: /talks/
---
<div class="posts">
  {% for talk in site.talks %}
    <article class="post">

      <div class="post-header">
        <h1><a href="{{ site.baseurl }}{{ talk.url }}">{{ talk.title }}</a></h1>
      </div>

      <div class="entry">
        {{ talk.summary }}
      </div>
    </article>
  {% endfor %}
</div>
