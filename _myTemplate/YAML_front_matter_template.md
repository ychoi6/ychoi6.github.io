---
title: 
permalink: /
tag: 
layout: post
author: Yoonmi Choi
published: true
siteNav: true
---

---
title: 
permalink: /
tag: 
layout: post
author: Yoonmi Choi
published: true
parent: title of parent file
grand_parent: title of parent file
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


# subtitles
### Sub title 



# Markdown

```markdown
text
```

```markdown

# tip box

> ##### TIP
>
> This guide is last tested with @napi-rs/canvas^0.1.20, so make sure you have
> this or a similar version after installation.
{: .block-tip }