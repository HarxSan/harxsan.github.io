---
layout: page
title: Open-model architecture tracker
description: Config-level architecture facts for open-weight model releases, sourced from each model's own config.json and safetensors headers.
permalink: /models/
---

Config-level architecture facts for open-weight model releases, sourced from each model's own config.json and safetensors headers.

<ul>
{% for model in site.models %}
  <li><a href="{{ model.url }}">{{ model.title }}</a></li>
{% endfor %}
</ul>
