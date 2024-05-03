---
layout: page
title: talks
permalink: /talks/
description: here you can find talks I made in different events
nav: true
nav_order: 7
display_categories: [work, fun]
horizontal: false
---

<!-- pages/talks.md -->
<div class="talks">
  {% if site.enable_talk_categories and page.display_categories %}
    <!-- Display categorized talks -->
    {% for category in page.display_categories %}
      <a id="{{ category }}" href=".#{{ category }}">
        <h2 class="category">{{ category }}</h2>
      </a>
      {% assign categorized_talks = site.talks | where: "category", category %}
      {% assign sorted_talks = categorized_talks | sort: "date" | reverse %}
      <!-- Generate cards for each talk -->
      {% if page.horizontal %}
        <div class="container">
          <div class="row row-cols-2">
            {% for talk in sorted_talks %}
              {% include talks_horizontal.liquid %}
            {% endfor %}
          </div>
        </div>
      {% else %}
        <div class="grid">
          {% for talk in sorted_talks %}
            {% include talks.liquid %}
          {% endfor %}
        </div>
      {% endif %}
    {% endfor %}
  {% else %}
    <!-- Display talks without categories -->
    {% assign sorted_talks = site.talks | sort: "date" | reverse %}
    <!-- Generate cards for each talk -->
    {% if page.horizontal %}
      <div class="container">
        <div class="row row-cols-2">
          {% for talk in sorted_talks %}
            {% include talks_horizontal.liquid %}
          {% endfor %}
        </div>
      </div>
    {% else %}
      <div class="grid">
        {% for talk in sorted_talks %}
          {% include talks.liquid %}
        {% endfor %}
      </div>
    {% endif %}
  {% endif %}
</div>
