---
title: Projects
icon: <i class="far fa-rocket mr-2"></i>
layout: page
permalink: /projects/
---
{% assign sorted_projects = site.collection_projects | sort: 'sort_order' %}
{% for project in sorted_projects %}

<div class="row {% if project.sort_order != 1 %}mt-4{% endif %}">

    <div class="card">
        <div class="card-body">
            <h2 class="card-title"><img class="mr-2 align-middle" src="{{ project.icon | prepend: site.images-path | prepend: site.baseurl | prepend: site.url }}" alt="{{ project.title}}" height="28" />{{ project.title }}</h2>
            <p class="card-text">{{ project.desc }}</p>
            <a href="{{ project.href }}" class="btn btn-info btn-block" target="_blank"><i class="{{ project.href_icon_class }} mr-2"></i>View Project</a>
        </div>
    </div>

</div>

{% endfor %}