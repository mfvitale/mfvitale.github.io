---
layout: page
title: Must read books
permalink: /must-read-books/
sitemap: true
---

<ul>
        {% for item in site.data.menu.pages %}
            <li><a href="{{ item.link }}">{{ item.name }}</a></li>
        {% endfor %}
      </ul>

