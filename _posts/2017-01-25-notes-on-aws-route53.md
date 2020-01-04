---
layout: post
title:  "AWS Route 53 Notes"
number: 25
date:   2017-01-25 0:00
categories: networking
---
## About DNS
DNS is a globally distributed service that translates human readable domain names (like www.ayushsharma.in) into their corresponding IP addresses (like 104.27.150.240) of machines where the servers actually live. The servers are responsible for delivering the content that we want to see, and the DNS system is how we get there. This request to resolve a domain name into its numerical IP address is called a "query".

### How does DNS route traffic to your web application?
<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}how-dns-works-for-your-website.jpg" width="582" height="445" alt="How DNS works for your website">

1. A user opens a web browser, enters www.example.com in the address bar, and presses Enter.
1. The request for www.example.com is routed to a DNS resolver, which is typically managed by the user's Internet service provider (ISP), such as a cable Internet provider, a DSL broadband provider, or a corporate network.
1. The DNS resolver for the ISP forwards the request for www.example.com to a DNS root name server.
1. The DNS resolver for the ISP forwards the request for www.example.com again, this time to one of the TLD name servers for .com domains. The name server for .com domains responds to the request with the names of the four Amazon Route 53 name servers that are associated with the example.com domain.
1. The DNS resolver for the ISP chooses an Amazon Route 53 name server and forwards the request for www.example.com to that name server.
1. The Amazon Route 53 name server looks in the example.com hosted zone for the www.example.com record, gets the associated value, such as the IP address for a web server, 192.0.2.44, and returns the IP address to the DNS resolver.
1. The DNS resolver for the ISP finally has the IP address that the user needs. The resolver returns that value to the web browser. The DNS resolver also caches (stores) the IP address for example.com for an amount of time that you specify so that it can respond more quickly the next time someone browses to example.com. For more information, see time to live (TTL).
1. The web browser sends a request for www.example.com to the IP address that it got from the DNS resolver. This is where your content is, for example, a web server running on an Amazon EC2 instance or an Amazon S3 bucket that's configured as a website endpoint.
1. The web server or other resource at 192.0.2.44 returns the web page for www.example.com to the web browser, and the web browser displays the page.

### Types of DNS systems
<strong>Recursive DNS</strong>: Clients typically first connect to a Recursive DNS which knows where to get the corresponding IP address for a domain. It will connect to an Authoritative DNS to get this information, then cache this information for a period of time. Recursive DNS does not itself have the target IP addresses.

<strong>Authoritative DNS</strong>: This type of DNS is the final authority on DNS information. They keep the IP addresses for domain names, and provide an update mechanism for changing those values.

### About Domain Names
DNS uses a hierarchical name structure, separate by a "." (dot). In "www.example.com", "com" is the TLD (top-level domain) and "example" is the second-level domain. Second-level domains are members of the TLD. The second level domain has the following restrictions:

1. Must use characters between a-z, numbers between 0-9, and hyphen ("-") character.
1. Cannot start or end with a hyphen.

A sub-domain is the third element that can be added to a domain. "www" is a popular sub-domain, as in the example URL mentioned above.
The TLD, the second-level domain and the sub-domain each cannot be more than 63 bytes long. The entire domain name cannot be more than 255 bytes long.


## Route 53
Route 53 is an Authoritative, highly available and scalable cloud DNS management service provided by AWS. It is reliable and cost-effective. It is compatible with IPv6 also. Route 53 allows management of mappings between domain names and IP addresses (records), and replies to "queries" for translating domain names to their corresponding IP addresses.
Route 53 does its routing magic on port 53, hence the name.

### Features:

#### DNS Service
1. Weight-based routing: Weights can be assigned to different record sets for the same DNS name to manage the proportion of traffic to be routed to a given record set. For example, one record can have a weight of 1, and another a weight of 3, meaning the first record will be returned 25% of the time. This is useful for doing A/B testing, by routing a portion of the traffic to another cluster. Weights can have values between 0 and 255.
1. Latency-based routing: Users will be routed to the AWS region that provides the lowest latency.
1. Geo-routing: Users will be routed to different endpoints based on where the request comes from. This is useful for serving localised content, such as serving content in the right language or restricting content to only certain locations. Routing can be localised by continent, country, and state. Geo-routing can be combined with latency-based routing and DNS failover. A global record can be created which will be returned in case Route 53 can't figure out where the request comes from, or if you haven't configured that location. Records can also overlap, and Route 53 will return the most specific record. If it doesn't find one, it will travel up and return the first one it finds. First it will return the record for the state, if found, or the country, or the continent, or finally the global record.
1. Traffic flow: Route users to best location based on latency, geography, target health, etc.
1. Private DNS: Route 53 can also manage private addresses, and will only resolve those domains if they come from within the specified VPC.
1. DNS failover: Route 53 will monitor health of applications and route requests away from unhealthy resources. Useful for creating backup sites.
1. Multiple IPs can be associated with a single record.
1. Route 53 propagates DNS changes within 60 seconds, based on network conditions.

#### Others
1. Route 53 has SLA of 100%, otherwise you are eligible for a service credit.
1. Health checks and monitoring: Route 53 will monitor the health of applications using configurations that we make.  When health checks fail, Route 53 will disable that endpoint for the amount of time specified in the TTL for that record set, so specify shorter TTLs, ideally around 60 seconds. There is no load-balancing based on target health, that's what ELBs are for. Note that if all health checks for all endpoints are failing, Route 53 will behave as if they are all passing, and route traffic to them.
1. With Route 53 you can register and manage new domains.
1. Traffic Flow is a visual editor that allows you to create complex routing policies spanning multiple regions and environments.

### Hosted Zones
A hosted zone is a Route 53 concept which designed to allow easy management of multiple domain names and records. Each hosted zone is created for a second-level domain, such as ayushsharma.in, and can in turn contain records and sub-domains for that second-level domain. Route 53 allows creation of multiple hosted zones for the same second-level domain, allowing for the creation of "test" and "production" set ups.

### Aliases
Alias records are Route 53-specific records that are basically CNAMES for internal AWS resources. They differ from CNAMES because:

1. Aliases can only be used to map to internal AWS resources, like ELB's, CloudFront distributions, Elastic Beanstalk environments, and S3 buckets.
1. Unlike CNAMEs, Alias records exist only inside Route 53.
1. They are not visible to resolvers.
1. You can create an Alias of the zone apex, but not a CNAME.
1. Alias queries are free.

### Pricing:

#### Hosted Zones
1. $0.5 per hosted zone per month for first 25 zones, $0.1 per hosted zone per month after that. Hosted zones deleted within 12 hours of creation are not charged. Not prorated for partial month; prices are charged on setup and on first day of subsequent month.

#### Traffic Flow
1. $50 per policy record per month. Prorated for partial month.
1. No charges for policy record not associated with a DNS name via policy record.

#### DNS Queries
1. Standard queries: $0.4/million for first 1 billion queries per month, $0.2/million after that.
1. Latency-based queries: $0.6/million for first 1 billion queries per month, $0.3/million after that.
1. Geo queries: $0.7/million for first 1 billion queries per month, $0.35/million after that.
1. Queries to Alias records mapped to the following are free: ELB, CloudFront distributions, Elastic Beanstalk environments, S3 buckets.

#### Health Checks
1. Basic health checks: $0.5 per health check per month for AWS endpoints, $.75 per health check per month for non-AWS end points.
1. Additional features include HTTPS, string matching, fast interval, and latency measurement. They cost $1 per feature per month for AWS endpoints, $2 per feature per month for non-AWS endpoints.

#### Domains
1. Monthly charge for domain names registered with Route 53.