---
layout: page
permalink: /
---
<div class="row">

    <div class="col-12 col-lg-8">
        {% for post in site.posts %}
        {% if forloop.index > 6 %}
        {% break %}
        {% endif %}
        <h1 style="font-size: 1.4rem;" class="py-1 mb-3">
            <a class="post-link" href="{{ post.url | prepend: site.baseurl | prepend: site.url }}" title="{{ post.title }}">{{ post.title }}</a>
            <span class="badge-date">{{ post.date | date: "%b %-d, %Y" }}</span>
        </h1>
        {% endfor %}
        <p class="text-center">
            <a class="btn btn-block btn-primary" href="/notes"><i class="far fa-book-open mr-2"></i>Read more notes</a>
        </p>
    </div>
    <div class="col-12 col-lg-4">
        <img class="embed-responsive rounded" src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}ayush-sharma.jpg" with="340" height="277" alt="Ayush Sharma">
        <p class="mt-2 text-secondary">Hello :) My name is Ayush Sharma. I love building things for the web.</p>
        <h5 class="mt-4"><i class="far fa-rocket mr-2"></i>My Projects</h5>
    
        {% assign sorted_projects = site.collection_projects | sort: 'sort_order' %}
        {% for project in sorted_projects %}
    
        <div{% if project.sort_order != 1 %} class="mt-2"{% endif %}>
        <a class="btn btn-light btn-block" style="text-align:left" href="{{ project.href }}" title="{{ project.title}}" target="_blank">
        <img class="mr-0" src="{{ project.icon | prepend: site.images-path | prepend: site.baseurl | prepend: site.url }}" alt="{{ project.title}}" width="20" height="20" title="{{ project.title}}" />
        {{ project.title }}
        </a>
        </div>
    
        {% endfor %}
        </div>
    </div>

