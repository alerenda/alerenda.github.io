---
layout: page
permalink: /publications/
title: publications
description: Check my <a target="_blank" href="https://scholar.google.it/citations?user=13nYgdUAAAAJ&hl=it"><b>scholar profile</b></a> for an updated list.
years: [2026,2025,2024,2023,2022,2021,2020,2019]
nav: true
order: 2
--- 

<div class="publications">

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
