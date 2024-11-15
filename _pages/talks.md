---
layout: pageMap
title: talks
permalink: /talks/
description: Dissemination activity, participation at international conferences with contributed talks, and seminars.
nav: true
display_categories: [invited talks - round tables, contributed talks, project meeting, other attended conferences, seminars]
order: 4
location:
  latitude: 51.5285582
  longitude: -0.2416807
---
{% leaflet_map %}
    {% for category in page.display_categories %}
      {% assign categorized_talks = site.talks | where: "category", category %}
      {% for talk in categorized_talks %}
            {% leaflet_marker { "latitude" : {{talk.latitude}},
                                "longitude" : {{talk.longitude}},
				"popupContent" : "{{talk.sigla}}" } %}
      {% endfor %}
    {% endfor %}
{% endleaflet_map %}

<div class="talks">
  <!-- Display categorized projects -->
    {% for category in page.display_categories %}
      <h2 class="category">{{category}}</h2>
      {% assign categorized_talks = site.talks | where: "category", category %}
      {% assign sorted_talks = categorized_talks | sort: "importance" | reverse %}
      <!-- Generate cards for each project -->
		<div class="container">
		  <div class="row">
		  {% for talk in sorted_talks %}
			{% include talks_horizontal.html %}
		  {% endfor %}
		  </div>
		</div>
    {% endfor %}
</div>
