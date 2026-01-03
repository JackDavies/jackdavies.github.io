---
layout: page
title: Projects
order: 2
---

{%- for post in site.posts -%}
    {%- if post.category contains "Project" -%}
        <li>
            <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
            <p>{{post.excerpt}}</p>        
        </li>
    {%- endif -%}
{%- endfor -%}
