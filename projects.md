---
title: Projects
icon: <i class="far fa-rocket mr-2"></i>
layout: page
permalink: /projects/
---
<div class="row card-deck">

{% assign sorted_projects = site.collection_projects | sort: 'sort_order' %}
{% for project in sorted_projects %}

    <div class="card bg-primary">
        <div class="card-body">
            <h2 class="card-title"><img class="mr-2 align-middle" src="{{ project.icon | prepend: site.images-path | prepend: site.baseurl | prepend: site.url }}" alt="{{ project.title}}" height="28" />{{ project.title }}</h2>
            <p class="card-text">{{ project.desc }}</p>
            
        </div>
        <div class="card-footer">
            <a href="{{ project.href }}" class="btn btn-info btn-block" target="_blank"><i class="{{ project.href_icon_class }} mr-2"></i>View Project</a>
        </div>
    </div>

{% endfor %}

</div>