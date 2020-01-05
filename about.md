---
title: About Me
icon: <i class="far fa-coffee mr-2"></i>
layout: page
permalink: /about/
---
<div class="row">
	<div class="col col-md-9">
		<p>
			I was eleven years old when Yahoo first introduced email in India. I remember creating my account, sending emails to everyone I knew, and I was fascinated by how easy communication had become. My love affair with computers had begun.
		</p>

		<p>
			Over the next few years I became as deeply involved in the field as I could. I joined my high school’s computer competition team, then experimented with programming and web technologies, and later architected and deployed several enterprise-class applications with a team of awesome professionals. All of this opened new doors for me when it came to not only what I worked on, but how I worked on it. I've learnt a lot along the way, and I'm happy to say the journey is not yet over.
		</p>
	</div>
	<div class="col col-md-3">
		<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}ayush-sharma.jpg" width="240" height="188" alt="Ayush Sharma">
	</div>
</div>

## <i class="far fa-envelope-open mr-2"></i>Contact

My email address is: **ayush`[at-the-rate]`ayushsharma.in**.

I can also be be found on:

<div class="row">

{% assign sorted_links = site.collection_social | sort: 'sort_order' %}
{% for nav in sorted_links %}

<div class="col-12 col-md-2">

<a class="{{ nav.href_class }}" href="{{ nav.href }}" title="{{ nav.title }}" target="_blank"><img class="mr-1" src="{{ nav.icon | prepend: site.images-path | prepend: site.baseurl | prepend: site.url }}" width="35" height="35" alt="Ayush Sharma on {{ nav.title }}" />{{ nav.title }}</a>

</div>

{% endfor %}