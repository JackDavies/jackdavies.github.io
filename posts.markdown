---
layout: page
title: Posts
order: 1
---

{%- assign categories = site.categories | sort -%}
{%- for category in categories -%}

{%- assign categoryName = category[0] -%}
{%- assign categoryNumPosts = category[1] | size -%}
<h3 id="post_title">{{ categoryName }} ({{ categoryNumPosts }})</h3>

<ul>
    {% assign sorted_posts = category[1] | reversed %}
    {% for post in sorted_posts %}
    <li>
        <a href="{{ post.url }}">{{ post.title }}</a> -
        <time datetime="{{ post.date | date_to_xmlschema }}"
              itemprop="datePublished">{{ post.date | date: "%b %-d, %Y" }}</time>
    </li>
    {% endfor %}
</ul>

{%- endfor -%}
