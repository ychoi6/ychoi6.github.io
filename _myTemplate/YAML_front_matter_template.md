---
title: 
permalink: /
tag: 
layout: post
author: Yoonmi Choi
date: 2024-00-00
published: true
---

# How to write posts
https://jekyllrb.com/docs/posts/


# Hyperlink
<a href="..."> ... </a>  


# Code block
```yaml
...

```

# Displaying an index of posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

# Post URL
{% post_url 2010-07-21-name-of-post %}
[Name of Link]({% post_url 2010-07-21-name-of-post %})