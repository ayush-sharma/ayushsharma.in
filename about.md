---
title: About Me
icon: <i class="far fa-coffee mr-2"></i>
layout: page
permalink: /about/
---
<div class="row">
	<div class="col col-md-9">
		<p>
			Ayush Sharma is a software architect, technical blogger, and fiction writer.
		</p>
		<p>
			He has been a software professional for over a decade, working with startups and large enterprises on cloud innovations, and writes about his experiences online on his blog.
		</p>
		<p>
			Attracted to writing fiction at an early age, he composed a science fiction novella and several short stories. He now devotes more time toward cultivating his writing skills.
		</p>
		<p>
			He lives with his family in New Delhi, India.
		</p>
		<p>
			His blog can be found at <a href="/">https://ayushsharma.in</a>.
		</p>
	</div>
	<div class="col col-md-3">
		<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}ayush-sharma.jpg" width="240" height="188" alt="Ayush Sharma">
	</div>
</div>

## <i class="far fa-envelope-open mr-2"></i>Contact

My email address is: **ayush[at-the-rate]ayushsharma.in**.

I can also be be found on:

<div class="row">

{% assign sorted_links = site.collection_social | sort: 'sort_order' %}
{% for nav in sorted_links %}

<div class="col-12 col-md-2">

<a class="{{ nav.href_class }}" href="{{ nav.href }}" title="{{ nav.title }}" target="_blank"><img class="mr-1" src="{{ nav.icon | prepend: site.images-path | prepend: site.baseurl | prepend: site.url }}" width="35" height="35" alt="Ayush Sharma on {{ nav.title }}" />{{ nav.title }}</a>

</div>

{% endfor %}