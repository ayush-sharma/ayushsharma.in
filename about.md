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

## <i class="far fa-toolbox mr-2"></i>Toolbox
- <span class="text-muted">Front-end Development:</span> HTML5, CSS3, Javascript, Bootstrap, JQuery, AJAX
- <span class="text-muted">Backend Development:</span> PHP, Python, Lua, Bash, Laravel, Jekyll, Boto3
- <span class="text-muted">Servers & Orchestration:</span> Nginx, Ansible, Packer, Terraform, Vagrant
- <span class="text-muted">Databases:</span> MySQL, SQLite, BigQuery, Aerospike, InfluxDB
- <span class="text-muted">Cloud Platforms:</span> AWS, Google Cloud, DigitalOcean, CloudFlare, Netlify
- <span class="text-muted">Monitoring & Logging:</span> Zabbix, Dynatrace, Collectd, Fluentd, Grafana, Papertrail
- <span class="text-muted">Source Control:</span> Git, Bitbucket Pipelines, GitHub
- <span class="text-muted">APIs:</span> Google APIs, PayTM, CashFree, ChartJS, MSG91
- <span class="text-muted">Virtualisation:</span> Docker, VMWare, VirtualBox
- <span class="text-muted">Operating Systems:</span> Ubuntu, Debian, Mac OS X, Windows

## <i class="far fa-history mr-2"></i>Work Experience
- <span class="text-muted">Chief Technology Officer</span> - ThinkCurve Technologies (October 2017 - October 2018)
- <span class="text-muted">Senior Software Engineer</span> - Vdopia, Inc. (February 2016 – October 2017)
- <span class="text-muted">Lead Web Developer</span> - LoudCell Technologies (July 2010 – October 2015)
- <span class="text-muted">Freelancer Web Developer</span> - (July 2009 – May 2010)

## <i class="far fa-rocket mr-2"></i>Projects

<ul>

{% assign sorted_projects = site.collection_projects | sort: 'sort_order' %}
{% for project in sorted_projects %}

<li><a href="{{ project.href }}" target="_blank">{{ project.title }}</a>: {{ project.desc }}</li>

{% endfor %}

</ul>

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