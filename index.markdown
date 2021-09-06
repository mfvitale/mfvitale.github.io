---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

 <ul>
        {% for item in site.data.menu.pages %}
            <li><a href="{{ item.url }}">{{ item.title }}</a></li>
        {% endfor %}
      </ul>
