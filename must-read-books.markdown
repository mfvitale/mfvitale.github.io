---
layout: page
title: Must read books
permalink: /must-read-books/
sitemap: true
---

<ul>
{% for book in site.data.book-list.books %}
    <li><a href="{{ book.url }}">{{ book.title }}</a></li>
{% endfor %}
</ul>
