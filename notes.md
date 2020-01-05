---
title: Notes
icon: <i class="far fa-book-open mr-2"></i>
layout: page
permalink: /notes/
---
{% assign post_years = "" %}
{% for post in site.posts %}
	{% assign year = post.date | date: '%Y' | append: "," %}
	{% assign post_years = post_years | append: year %}
{% endfor %}

{% assign post_years = post_years | split: "," | uniq %}


<div class="row">
	<div class="col">
		{% for post_year in post_years %}
			<div class="row">
				<div class="col">
					<h2{% unless forloop.first %} class="mt-4"{% endunless %}>{{ post_year }}</h2>
				</div>
			</div>
			{% assign posts = site.posts | sort: 'date' | reverse %}
			{% for post in posts %}
				{% assign year = post.date | date: '%Y' %}
				{% if year != post_year %}
					{% continue %}
				{% endif %}
				<div class="row mt-1 mb-1">
					<div class="col-12">
						<p>
							<a href="{{ post.url }}">{{ post.title }}</a>
							<span class="badge-date">{{ post.date | date: '%e %b' }}</span>
						</p>
					</div>
				</div>
			{% endfor %}
		{% endfor %}
	</div>
</div>
