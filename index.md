---
layout: page
permalink: /
---
<div class="row mb-4">

    <div class="col">
        <p class="display-3 text-center">Hi, my name is Ayush Sharma</p>
        <p class="lead text-center">I write about technology, build software to solve problems, and help people speak Cloud.</p>
    </div>

</div>

<div class="row">
    
    <div class="col">
        <p class="lead">My recent work</p>
        {% for post in site.posts %}
        {% if forloop.index > 6 %}
        {% break %}
        {% endif %}
        <h1 style="font-size: 1.4rem;" class="py-1 mb-3">
            <a class="post-link" href="{{ post.url | prepend: site.baseurl | prepend: site.url }}" title="{{ post.title }}">{{ post.title }}</a>
        </h1>
        {% endfor %}
        <p class="text-center">
            <a class="btn btn-primary" href="/notes"><i class="far fa-book-open mr-2"></i>Read more</a>
        </p>
    </div>
