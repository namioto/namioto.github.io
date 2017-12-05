---
layout: page
title: Tags
permalink: /tags/
sitemap:
  priority: 0.7
---
{{site.tags}}
{% for tag in site.tags %}
* **[{{ tag.name }}]({{ site.baseurl }}/tags/{{ tag.name }})** {% if tag.description %}- {{tag.description}}{% endif %}
{% endfor %}
