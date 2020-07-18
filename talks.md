---
layout: page
title: Talks
permalink: /talks/
---
<div>Testing me</div>

<div class="posts">
  {% for talk in site.talks %}
    <article class="post">

      <div class="post-header">
        <h1><a href="{{ site.baseurl }}{{ talk.url }}">{{ talk.title }}</a></h1>
        <h5 class="dated">{{ post.date | date: "%B %e, %Y" }}<h5>
      </div>

      <div class="entry">
        {{ talk.summary }}
      </div>
    </article>
  {% endfor %}
</div>
