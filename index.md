---
title: Blog Index
layout: default
---
Welcome to my blog.
For more, see my website at [jamesam.uk](https://jamesam.uk).

<ul>
{% for post in site.posts %}
    <li>
        <a href="{{ post.url }}">{{ post.title }}</a>
        {{ post.date | date_to_long_string }}

        {{ post.excerpt }}
        <a href="{{ post.url }}">Keep reading...</a>
    </li>
{% endfor %}
</ul>
