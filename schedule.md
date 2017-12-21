---
layout: page
title: Weekly (tentative) Schedule (For a 3-Credit course)
group: Schedule
---

<table class="table table-striped table-hover table-bordered">
  <tr>
    <th>Week</th>
    <th>Date</th>
    <th>Topics</th>
    <th>Notes</th>
  </tr>
{% for item in site.data.spreadsheets.schedule %}
  <tr>
    <td>{{ item.week }}</td>
    <td>{{ item.date }}</td>
    <td>{{ item.topics }}</td>
    <td>{{ item.notes | default: "&nbsp;" }}</td>
  </tr>
{% endfor %}

<!-- Schedule updated on {{ site.data.spreadsheets_updated.schedule }} -->
