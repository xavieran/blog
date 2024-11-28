---
title: Documentation
---

Documentation for various systems of Betrayal at Krondor
<ul>
  {% for page in site.pages %}
    {% if page.url contains '/content/' and page.url != '/content/' %}
      <li><a href="{{ site.baseurl }}{{ page.url | remove site.baseurl}}">{{ page.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>
