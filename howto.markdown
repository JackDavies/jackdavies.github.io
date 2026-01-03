---
layout: page
title: HowTo
order: 3
---

{%- for post in site.posts -%}
    {%- if post.category contains "HowTo" -%}
        <li>
            <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
            <p>{{post.excerpt}}</p>        
        </li>
    {%- endif -%}
{%- endfor -%}
