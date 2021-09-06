---
layout: page
title: Must read books
permalink: /must-read-books/
sitemap: true
---

{% for book in site.data.book-list.books %}
    {% if forloop.index | modulo: 2 %}
        <div class="blog-card alt">
       {% else %}
        <div class="blog-card alt">
    {% endif %}
            <div class="meta">
              <div class="photo" style="background-image: url(https://storage.googleapis.com/chydlx/codepen/blog-cards/image-1.jpg)"></div>
            </div>
            <div class="description">
              <h1>{{ book.title }}</h1>
              <h2>{{ book.subtitle }}</h2>
              <p> {{ book.description }}</p>
              <p class="read-more">
                <a href="{{ book.url }}">Buy</a>
              </p>
            </div>
          </div>
    </div>
{% endfor %}
</ul>
