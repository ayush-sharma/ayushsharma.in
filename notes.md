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
  <div class="col-3">
    <div class="nav flex-column nav-pills" id="v-pills-tab" role="tablist" aria-orientation="vertical">
	 	{% for post_year in post_years %}
			<a class="nav-link{% if forloop.first %} active{% endif %}" id="v-pills-{{ post_year }}-tab" data-toggle="pill" href="#v-pills-{{ post_year }}" role="tab" aria-controls="v-pills-{{ post_year }}" aria-selected="{% if forloop.first %}true{% else %}false{% endif %}">{{ post_year }}</a>
		{% endfor %}
    </div>
  </div>
  <div class="col-9">
    <div class="tab-content" id="v-pills-tabContent">
	{% for post_year in post_years %}
      	<div class="tab-pane fade {% if forloop.first %}show active{% endif %}" id="v-pills-{{ post_year }}" role="tabpanel" aria-labelledby="v-pills-{{ post_year }}-tab">
	  		<h2>{{ post_year }}</h2>
			{% assign posts = site.posts | sort: 'date' | reverse %}
			{% for post in posts %}
				{% assign year = post.date | date: '%Y' %}
				{% if year != post_year %}
					{% continue %}
				{% endif %}
				<div class="row">
					<div class="col-12">
						<p>
							<a href="{{ post.url }}">{{ post.title }}</a>
							<span class="small"> | {{ post.date | date: '%e %b' }}</span>
						</p>
					</div>
				</div>
			{% endfor %}
		</div>
	{% endfor %}
    </div>
  </div>
</div>