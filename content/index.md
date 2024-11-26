---
title: Documentation
---
![bak_logo](http://xavieran.github.io/BaKHelpWeb/notendur.hi.is/eybjorn/krondor/kronlogb.gif?raw=true "BaK Logo")
<!--{% if page.url contains '/content/' and page.url != '/content/' %}   {% endif %}-->
<ul>
  {% for page in site.pages %}
    
      <li><a href="{{ page.url }}">{{ page.title }}</a></li>
 
  {% endfor %}
</ul>
