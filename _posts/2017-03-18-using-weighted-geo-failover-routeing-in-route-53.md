---
layout: post
title:  "Using Weighted, Geo and Fail-over Routeing in Route 53"
number: 33
date:   2017-03-18 0:00
categories: reliability
---

I've already written some notes about Route 53. It's pretty feature-rich, allowing you to set up weighted, geo, latency, and fail-over routeing. Setting these up is pretty straightforward. All you need to do is create multiple entries with the same name and type, but different routeing rules. When it comes time for Route 53 to resolve those entries, it will parse your routeing rules and return the appropriate response.

For example, to create weighted routeing for example.com, you would create 2 A-record entries for example.com, but with different weights. One could have a weight of 1, and the other a weight of 2. So when Route 53 needs to resolve, it will look at entries with the same name and type, and find our example.com A records. It would then parse the routeing rules, see that one record needs to be returned twice as many times as the other, and pick the appropriate one. This is routeing 101 in Route 53. For more complex configurations, we have to combine the different routing rules in a tree-like configuration.

Consider this complex scenario for example.com:

- We want some some users to go to one.example.com, but twice as many users to two.example.com.
- For two.example.com, we want users from the EU to go to ELB1, and Asia users at ELB2.
- Additionally, in case the EU ELB is down, we want users to go to the Asia ELB, and vice versa.

To implement this, we would have 3 levels.

- At the first level, we would have weighted routing for one.example.com and two.example.com. Remember they need to have the same name and type. The first will have a weight of 1, the second a weight of 2.
- At the second level, we would have geo routeing to split traffic based on location, between EU and Asia. Here too they need to have the same name and type.
- Beneath that, at the third level, we would have fail-over routeing to switch ELBs in case one of them is down. Same name and type here as well. For EU, ELB1 will be the Primary, and ELB2 will be the Secondary. Vice versa for Asia.

The flow would look like this:
<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}aws-route-53-configuration-using-geo-weighted-and-fail-over-routeing.jpg" width="766" height="900" alt="Amazon Route 53 configuration using Geo, Weighted, and Fail-over routeing.">