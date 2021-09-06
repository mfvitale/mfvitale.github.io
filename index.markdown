---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

 <ul>
        {% for book in site.data.book-list.books %}
            <li><a href="{{ book.url }}">{{ book.title }}</a></li>
        {% endfor %}
      </ul>
