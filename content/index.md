---
title: Documentation
---

Documentation for various systems of Betrayal at Krondor
<ul>
  {% for page in site.pages %}
    {% if page.url contains '/content/' and page.url != '/content/' %}
      <li><a href="{{ page.url }}">{{ page.title }}</a></li>
      {{page.url| inspect }}
    {% endif %}
  {% endfor %}
</ul>
