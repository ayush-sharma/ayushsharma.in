---
layout: post
title:  "Gartner's Magic Quadrants: A summary of cloud Infrastructure-as-a-Service providers over the last 5 years"
number: 71
date:   2018-09-16 0:00
categories: cloud
---
I recently read Gartner's "Magic Quadrant for Cloud Infrastructure as a Service, Worldwide" for 2018, which considers the leading cloud IaaS providers' market positions and evaluates their strengths and weaknesses, and I was tempted to go further back and review what had happened in the Magic Quadrants over the last 5 years. Below is a summary of these reports.

## What is Infrastructure-as-a-Service (IaaS)?
A cloud IaaS vendor, such as Amazon Web Services or Microsoft Azure, provides self-service access to programmatically accessible and highly automated infrastructure resources, such as compute servers, storage, and networking. These resources are scalable and elastic on-demand and in near-real-time, and are metered by use.

In IaaS, the provider manages the data center facilities, hardware and virtualization, but everything above the hypervisor layer — the operating system, middle-ware and application — is managed by the customer.

## What is Gartner's Magic Quadrant for IaaS?
Gartner's "Magic Quadrant for Cloud Infrastructure as a Service, Worldwide" reports are a much-awaited market analysis of the profiles of the individual cloud vendors, and of the cloud market overall.

Gartner evaluates the vendors based on **ability to execute**, which includes the features provided, self-service and automation capabilities, their product road-map and overall strategy, and **completeness of vision**, which includes the vendors' understanding of the cloud market, their position in this market, competitive differentiation, value proposition and solution strategy, financial investments in the future of their business, and expansion plans.

With the above criteria measured for every vendor, they are placed into one of four categories, or “quadrants”:

**Leaders**: Leaders are those who execute well today and are well-positioned for tomorrow. They distinguish themselves by having services suitable for strategic adoption and having an ambitious road-map. They have a track record of successful delivery, significant market share, and many reference-able customers.

**Challengers**: Challengers execute well today or may dominate a large segment, but do not yet fully understand the market direction. They are well-positioned to serve some current market needs, and have a track record of successful delivery for a particular set of use-cases, but are not adaption quickly enough to market challenges or do not have an ambitious road-map.

**Visionaries**: Visionaries do not execute well today, but have a strong sense of where the market is headed. They have an ambitious road-map and are making significant investments in technology and their services are still emerging. They may not serve a broad range of use-cases yet.

**Niche Players**: Niche players focus successfully on small segments and do not outperform others. Niche Players are excellent providers for specific use-cases but do not serve the general market well.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service.">

I'm going to talk about the IaaS Magic Quadrants for 2014, 2015, 2016, 2017, and 2018 below. Given that Amazon Web Services, Microsoft and Google eventually dominate the market in 2018, I'm going to summarize their strengths and weaknesses from past reports.

## Magic Quadrant for 2014

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide-2014.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service for 2014.">

The 15 cloud vendors in this Magic Quadrant were evaluated as follows:

- Leaders: Amazon Web Services, Microsoft.
- Challengers: None.
- Visionaries: CenturyLink, CSC, Google, IBM, Verizon Terramark.
- Niche players: Dimension Data, Fujitsu, GoGrid, HP, Joyent, Rackspace, Virtustream, VMware.

### Amazon Web Services
Strengths:

- Market share leader, thought-leader, and exceptionally agile.
- More than 5 times the compute capacity of the remaining 14 vendors in this Magic Quadrant.
- Diverse customer base, suitable for a broad range of use-cases.
- Very large technology partner ecosystem.
- Marketplace available with licensed software integrated with Amazon Web Services ecosystem.

Weaknesses:

- Charges separately for optional items that are usually bundled in competitor offerings, which increases billing and auditing complexity.
- Support is separate and does not depend on size-of-spend; enterprise-level support has dedicated account manager but carries up to 10% premium on total Amazon Web Services spend
- Faces stiff competition from Microsoft in traditional IT market and Google in the cloud-native market.

### Microsoft
Strengths:

- It has a global vision of cloud infrastructure and platform services, and is aggressively expanding in international markets.
- Its brand, existing customer relationships, deep investments in engineering, and aggressive road-map are enabling it to grow and iterate quickly.
- Has pledged to maintain Amazon Web Services-comparable pricing for general public.
- User-interface is modern and easy-to-use, and will appeal to Windows admins and developers.

Weaknesses:

- Many features are still in "preview" or are coming soon.
- Customers who want to adopt Azure in 2 years or more should start now, others with more immediate needs will encounter challenges.
- Faces the challenge of getting its core infrastructure technology to operate at cloud-scale, managing it, and facilitating customers to move towards more automated infrastructure.
- Does not yet have a software licensing marketplace.
- Only beginning to build its partner ecosystem.
- Offerings are very Microsoft-centric and will appeal to .NET developers.
- Not a lot of enterprise Linux options.

### Google
Strengths:

- Has been productizing existing capabilities for other customers so they can "run like Google", rather than building things from scratch. So although it is a late entrant in this market, it will be able to innovate and iterate quickly.
- Its cloud platform represents little incremental cost to Google overall, meaning it can price aggressively.
- Has a better understanding of boundary between IaaS and PaaS, which will allow customers to choose between control and infrastructure automation better.
- Has its own high-capacity global network.
- Compute provisioning is extremely fast, typically under 1 minute.

Weaknesses:

- Its cloud is very new and lacks an operational track record.
- Still learning to engage with enterprise and mid-market customers; needs to earn the trust of businesses, especially ones that will want to migrate legacy apps rather than build new cloud-native apps.
- Needs to build a ecosystem around its cloud.
- Partner program is very nascent.


## Magic Quadrant for 2015

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide-2015.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service for 2015.">

The 15 cloud vendors in this Magic Quadrant were evaluated as follows:

- Leaders: Amazon Web Services, Microsoft.
- Challengers: None.
- Visionaries: CenturyLink, IBM , Google, VMware.
- Niche players: CSC, Dimension Data, Fujitsu, Interoute, Joyent, Rackspace, Verizon, Virtustream, NTT Communications.

### Amazon Web Services
Strengths:

- Market share leader, thought-leader, and exceptionally agile.
- More than 10 times the compute capacity of the remaining 14 vendors in this Magic Quadrant.
- Diverse customer base, suitable for a broad range of use-cases.
- Very large technology partner ecosystem.
- Marketplace available with licensed software integrated with Amazon Web Services ecosystem.
- Faces competition from Google and Microsoft, but retains multi-year competitive advantage.
- Has become the “safe choice”, appealing to customers that want a broad range of capabilities and long-term market leadership.

Weaknesses:

- Charges separately for optional items that are usually bundled in competitor offerings, which increases billing and auditing complexity. Use of third-party management tools is recommended.
- Support is separate and tiered, and does not depend on size-of-spend; enterprise-level support has dedicated account manager but carries up to 10% premium on total Amazon Web Services spend
- Services that don’t get much customer traction may not get the same depth of continued attention and investment.

### Microsoft
Strengths:

- Its IaaS and PaaS operate and feel like a unified whole. User-interface is modern and easy-to-use, and will appeal to Windows admins and developers.
- Rapidly rolling out new features, including differentiated capabilities.
- Twice as much compute capacity as remaining 13 vendors in this Magic Quadrant, excluding Amazon Web Services.
- Has pledged to maintain Amazon Web Services-comparable pricing for general public.

Weaknesses:

- Customers have expressed concerns over global impact of past outages, meaning Azure services will need non-Azure disaster recovery solutions.
- Customers who want to adopt Azure in 1 year or more should start now, others with more immediate needs will encounter challenges.
- Just beginning to build its partner ecosystem.
- Recently launched a marketplace.
- Has begun recruiting MSPs, but many of these lack experience with Azure platform.

### Google
Strengths:

- Has been productizing existing capabilities for other customers so they can "run like Google", rather than building things from scratch.
- Has its own high-capacity global network.
- Compute provisioning is extremely fast, typically under 1 minute.

Weaknesses:

- Still learning to engage with enterprise and mid-market customers; needs to earn the trust of businesses, especially ones that will want to migrate legacy apps rather than build new cloud-native apps.
- Needs to build a ecosystem around its cloud.
- Partner program is very nascent.
- Prospective customers have difficult getting attention of the sales staff and being directed toward appropriate solutions.
- Feature release velocity is not as high as expected.

**Vendors changes in 2015**
Added: Interoute, NTT Communications.
Dropped: GoGrid, HP. 

## Magic Quadrant for 2016

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide-2016.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service for 2016.">

The 10 cloud vendors in this Magic Quadrant were evaluated as follows:

- Leaders: Amazon Web Services, Microsoft.
- Challengers: None.
- Visionaries: Google.
- Niche players: CenturyLink, Fujitsu, IBM, NTT Communications, Rackspace, Virtustream, VMware.

### Amazon Web Services
Strengths:

- Market share leader, thought-leader, and exceptionally agile.
- Largest share of compute capacity; has attracted ecosystem of open-source tools.
- Diverse customer base, suitable for a broad range of use-cases.
- Very large technology partner ecosystem: Over 1,000 technology partners have licensed and packaged their software to run on Amazon Web Services.
- Faces competition from Google and Microsoft, but retains multi-year competitive advantage.
- Has become the “safe choice”, appealing to customers that want a broad range of capabilities and long-term market leadership.

Weaknesses:

- Charges separately for optional items that are usually bundled in competitor offerings, which increases billing and auditing complexity. Use of third-party management tools is recommended.
- Support is separate and tiered, and does not depend on size-of-spend; enterprise-level support has dedicated account manager but carries up to 10% premium on total Amazon Web Services spend
- Services that don’t get much customer traction may not get the same depth of continued attention and investment.
- New services are gradually rolled out across regions, meaning customers outside the US will get features slowly, and global customers may not get the same features in all regions.
- Organizations that cannot quickly take advantage of new features will suffer. Implemented best practices may become outdated as new capabilities are introduced. Less sophisticated customers may become overwhelmed.

### Microsoft
Strengths:

- Its IaaS and PaaS operate and feel like a unified whole. User-interface is modern and easy-to-use, and will appeal to Windows admins and developers.
- Rapidly rolling out new features, including differentiated capabilities.
- It is becoming more open and less reliant on Windows franchise. Azure support for Linux and other OSes is improving.
- While Azure is not as mature or feature-rich as Amazon Web Services, customers consider it “good enough” and base their vendor decision on factors other than technological capability.

Weaknesses:

- Not all features are implemented with the level of completeness, ease-of-use or API-enablement as desired.
- Documentation can be disorganized, incomplete, or sometimes out-of-date.
- It is still in the process of building the Azure ecosystem.

### Google
Strengths:

- Has been productizing existing capabilities for other customers so they can "run like Google", rather than building things from scratch.
- Has its own high-capacity global network.
- Compute provisioning is extremely fast, typically under 1 minute.
- Has pioneered infrastructure technology, like OS containers. Also advances container-oriented capabilities related to Kubernetes.
- Has a comprehensive vision for and extensive experience with cloud-native app life-cycle and deployments. This is driving GCP adoption.
- It is leveraging its expertise and experience with Big Data into product strengths in analytics and machine learning.

Weaknesses:

- Still learning to engage with enterprise and mid-market customers; needs to earn the trust of businesses, especially ones that will want to migrate legacy apps rather than build new cloud-native apps.
- Feature-set and scope are not as broad as the current market leaders.
- It is missing key capabilities that are important to both established players and startups, such as granular RBAC, software licensing marketplace, and complex network topologies.
appropriate solutions.
- Feature release velocity is not as high as expected.

**Vendors changes in 2016**
Added: CSC, Dimension Data, Interoute, Joyent, Verizon

## Magic Quadrant for 2017

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide-2017.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service for 2017.">

The 14 cloud vendors in this Magic Quadrant were evaluated as follows:

- Leaders: Amazon Web Services, Microsoft.
- Challengers: None.
- Visionaries: Alibaba Cloud, Google, IBM, Oracle.
- Niche players: CenturyLink, Fujitsu, Interoute, Joyent , NTT Communications, Rackspace, Skytap, Virtustream.

### Amazon Web Services
Strengths:

- Market share leader for over 10 years with end-of-2016 revenue run-rate of more than $14B.
- Thought-leader and reference point for all other competitors.
- Many enterprise customers now spending over $5M annually, and a few spending over $100M annually.
- Accelerating pace of innovation on top of already rich portfolio of services.
- Has become the “safe choice”, appealing to customers that want a broad range of capabilities and long-term market leadership.
- Ecosystem of more than 2,000 consulting partners that offer managed and professional services.

Weaknesses:

- Charges separately for optional items that are usually bundled in competitor offerings, which increases billing and auditing complexity. Use of third-party management tools is recommended.
- New services are gradually rolled out across regions, meaning customers outside the US will get features slowly, and global customers may not get the same features in all regions.
- Organizations that cannot quickly take advantage of new features will suffer. Implemented best practices may become outdated as new capabilities are introduced. Less sophisticated customers may become overwhelmed.

### Microsoft
Strengths:

- Very high growth rate with end-of-2016 revenue run rate at $3B.
- Rapidly rolling out new features, including differentiated capabilities.
- Many customers spend more than $500K per year, and a few exceed $5M in annual spending.
- Good strategic provider for Microsoft-specific technologies.
- Embracing open-source technologies (like Linux VMs) is vital and positive strategic shift.

Weaknesses:

- Customers report that the service feels less enterprise-ready.
- Issues have been cited with technical support, documentation, training, and breadth of partner ecosystem.
- Not all features are implemented with the level of completeness, ease-of-use or API-enablement as desired.
- Multiple generations of solutions with unclear guidance increases complexity.
- Dev-ops-oriented customers will face lack of support in some open-source tools.

### Google
Strengths:

- Has been productizing existing capabilities for other customers so they can "run like Google". Customer Reliability Program helps customers run their infrastructure like Google SREs do.
- Feature velocity and differentiation velocity, such as with BigQuery and CloudSpanner, continue to increase.
- Chosen by customers who are more open-source-centric or devops-centric.
- Has its own high-capacity global network.
- Compute provisioning is extremely fast, typically under 1 minute.

Weaknesses:

- Feature-set and scope are not as broad as the current market leaders.
- Typically chosen as secondary provider rather than a strategic provider.
- Currently data centers in only 5 countries, but plans to add 8 more in 2017.
- Only recently begun to build partner ecosystem,

**Vendors changes in 2017**
Added: Alibaba Cloud, Interoute, Joyent, Oracle, Skytap.
Dropped: VMware.

## Magic Quadrant for 2018

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}gartner-magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide-2018.png" width="730" alt="Gartner's Magic Quadrants for Infrastructure-as-a-service for 2018.">

The 6 cloud vendors in this Magic Quadrant were evaluated as follows:

- Leaders: Amazon Web Services, Microsoft, Google.
- Challengers: None.
- Visionaries: None.
- Niche players: Alibaba Cloud, IBM, Oracle.

The global market for cloud IaaS has consolidated around hyperscale service providers. Infrastructure and operations leaders should adopt strategically, but consider scenario-specific providers as well.

### Amazon Web Services
Strengths:

- Market share leader for over 10 years with end-of-2017 revenue run-rate of more than $20B.
- Many enterprise customers now spending over $5M annually, and a few spending over $100M annually.
- Has become the “safe choice”, appealing to customers that want a broad range of capabilities and long-term market leadership.
- Most mature, enterprise-ready provider, with strong track record of customer success and most useful partner ecosystem.
- Ecosystem of more than 2,000 consulting partners that offer managed and professional services.
- Broadest ecosystem of SaaS solutions and licensed software pre-integrated into Amazon Web Services.

Weaknesses:

- Charges separately for optional items that are usually bundled in competitor offerings, which increases billing and auditing complexity. Use of third-party management tools is recommended.
- Extensive portfolio of services requires significant expertise to manage.
- As less experienced MSPs are added to the MSP Partner Program, the MSP credential does not mean what it used to.
- Service updates are almost invisible, so customers have to choose to adopt new changes very quickly. Competitors have the option to improve on those service updates.

### Microsoft
Strengths:

- Very high growth rate with end-of-2016 revenue run rate at $4B.
- Rapidly rolling out new features, including differentiated capabilities.
- Many customers spend more than $500K per year, and a few exceed $5M in annual spending.
- Good strategic provider for Microsoft-specific technologies.
- Embracing open-source technologies (like Linux VMs) is vital and positive strategic shift.

Weaknesses:

- When operating at large scales, technical support personnel lack adequate understanding of Azure.
- Only just begun certifying MSPs, and existing Microsoft partners do not end up making great MSPs.
- Its optimized to be easy to use for new customers, making complex projects frustrating. 
- Multiple generations of solutions with unclear guidance increases complexity.
- Dev-ops-oriented customers will face lack of support in some open-source tools.
- Does not have the best reliability track record, especially with virtual networks. Impact can be reduced by meticulous monitoring and HA architecture, which requires additional resources and investments.

### Google
Strengths:

- Has been productizing existing capabilities for other customers so they can "run like Google". Customer Reliability Program helps customers run their infrastructure like Google SREs do.
- It is leveraging its expertise and experience with Big Data into product strengths in analytics and machine learning.

Weaknesses:

- Not a cost leader. Negotiations usually limited to single-year contracts.
- Small number of experienced MSPs adds additional cost and risk.

**Vendors changes in 2018**
Dropped: CenturyLink, Interoute, Fujitsu, Joyent, NTT Communications, Rackspace, Skytap, Virtustream.

## What Trends Are Currently Influencing Buyers?

1. Customer expectations continued to increase significantly over the course of 2017.
2. Integrated management capabilities and developer services are key generators of value. 
3. Managing multiple cloud IaaS providers is challenging. 
4. Customers frequently use third-party management tools for governance, especially multi-cloud governance. 
5. Application platform strategy, and the relevant vendor relationships, are important to many customers. 
6. Ecosystems are vital. 
7. Managed and professional services greatly increase the likelihood of a successful cloud IaaS implementation. 
8. "Lift and shift" migrations rarely achieve the desired business outcomes. Although many customers first investigate using IaaS to achieve cost savings, most customers buy IaaS to achieve greater business agility or to access infrastructure capabilities that they do not have within their own data center.
9. IaaS can drive significant cost savings when customers have short-term, seasonal, disaster recovery or batch-computing needs. It can also be a boon to companies with limited access to capital and to small companies - especially startups - that cannot afford to invest in infrastructure. - 10. Single-tenant options in public cloud IaaS are preferred over hosted private cloud IaaS.
11. Server-less computing is most easily adopted via public cloud services. The server-less model of computing abstracts the underlying infrastructure and hides many management considerations from the application developer.
12. Public cloud IaaS providers are beginning to deliver comprehensive solutions for micro-services infrastructure. Micro-services infrastructure is a composite of the various types of application infrastructure technologies used to build, deploy, run and manage micro-services and mini-services. 
13. Customers frequently acknowledge that public cloud IaaS providers offer superior security capabilities when compared to their own data centers.
14. APIs anchor a partner ecosystem. Programmatic (API) access to infrastructure is crucial, as it enables customers, as well as third parties, to build management tools for their platforms, and to enable applications to take maximum advantage of the infrastructure environment.

## What Key Market Aspects Should Buyers Be Aware Of?

1. The global market remains consolidated around two clear leaders, Amazon Web Services and Microsoft.
2. The remainder of the market is highly fragmented. Despite the thorough dominance of two market leaders, there are still thousands of service providers that offer cloud IaaS.
3. Chinese cloud providers have gone global, but still have limited success outside of the domestic Chinese market.
4. Customers normally prefer to keep data in-region for reasons of network latency. However, regulatory concerns that require keeping data in-country, as well as revelations about foreign intelligence agencies obtaining access to private data, have heightened the desire of non-U. S.-based customers to purchase cloud IaaS from local providers. 
5. Cloud IaaS is not a commodity. Providers vary significantly in their features, performance, cost and business terms. Although in theory, cloud IaaS has very little lock-in — a VM is just a VM, in the end — in truth, cloud IaaS is not merely a matter of hardware rental, but an entire data center ecosystem as a service.

## Conclusion
The market has become consolidated around two clear leaders, namely Amazon Web Services and Microsoft, with Google steadily catching up. While Amazon Web Services and Microsoft have retained the leadership position over the last 5 years, Google has moved from a Visionary to a Leadership quadrant. These three hyperscale providers will be important in the coming years, as they provide the broadest range of features and the most long-term market leadership. The rest of the market remains highly fragmented and variable, and will need to be considered thoroughly for use-case specific implementations.

## Further Reading

- [Magic Quadrant for Cloud Infrastructure as a Service, Worldwide 2014](https://www.scribd.com/document/285987098/Magic-Quadrant-for-Cloud-Infrastructure-as-a-Service-2014)
- [Magic Quadrant for Cloud Infrastructure as a Service, Worldwide 2015](https://virtualizationandstorage.files.wordpress.com/2015/06/magic-quadrant-for-cloud-infrastructure-as-a-service-worldwide.pdf)
- [Magic Quadrant for Cloud Infrastructure as a Service, Worldwide 2016](http://cloudmag.ro/wp-content/uploads/2016/08/Gartner-Reprint-MQ-IAAS-08-2016.pdf)
- [Magic Quadrant for Cloud Infrastructure as a Service, Worldwide 2017](https://gigas.com/blog/wp-content/uploads/2017/11/2017-Magic-Quadrant-for-Cloud-Infrastructure-as-a-Service-Gigas-pag-51.pdf)
- [Magic Quadrant for Cloud Infrastructure as a Service, Worldwide 2018](https://azure.microsoft.com/en-us/resources/gartner-iaas-magic-quadrant/)