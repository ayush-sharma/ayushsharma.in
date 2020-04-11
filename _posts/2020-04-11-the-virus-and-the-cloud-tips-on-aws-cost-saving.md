---
layout: post
title:  "​​The Virus ​😷​ and the Cloud ​⛅​: Tips on AWS cost-saving in these weird ​🤯​ times"
number: 88
date:   2020-04-11 00:00
categories: aws devops
---
> Unprecedented
> without previous instance; never before known or experienced; unexampled or unparalleled

I am coming to grips with our new situation in different ways. As I figure out how to work indoors for prolonged periods, another challenge has been to see a lot of organisations’ priorities shifting from being cloud-native to remote-native. And as this transition happens, I'm figuring out exactly how to operate a large-scale production environment in the age of asynchronous and minimalist communication.

This virus has impacted businesses in several ways and with nationwide lockdowns in effect, organisations are trying to reduce expenses in these uncertain times. Ops teams like mine are wrangling with cloud spend in new and more demanding ways.

I found some patterns while taming the cloud cost optimisation beast which I thought were worth sharing.

## Reinvent the wheel and roll your dashboard

Okay. So you’ve decided to reduce your cloud spend.

Awesome.

But you can't do this blind. So let's first talk about how to even look at this thing.

AWS provides Cost Explorer, QuickSight, and Billing for slicing and dicing your cloud spend.

Cost Explorer is great for simple stuff. It can answer "what" questions but not "where", "why" or "how" questions. You have filters for the account, region, service, usage, and more. But you cannot combine these in one view for any kind of useful drill-down for more precise answers.

AWS QuickSight is the only visualisation tool available in AWS. It allows you to create dashboards using a drag-and-drop interface and you can create a Cost Explorer 2.0 reasonably quickly. Rather than using Cost Explorer, which is standard across AWS customers, QuickSight can deliver personalised dashboards for your organisation's particular cloud habits. You can track different areas of visibility, and create dashboards for different teams based on resource tags so they can track their spend. QuickSight ingests data from the [Cost and Usage Report which I've written about before]({% post_url 2019-08-29-taming-aws-costs-with-cost-and-usage-reports-and-aws-athena %}).

Your AWS bill can answer "what" and "how much" questions because it provides details of quantities and per-unit charges that Cost Explorer doesn't. Comparing your current bill with your last one can help you identify anomalies where your cost is growing exponentially. Just scrolling through it can show you increasing on-demand vs Reserved server costs or increasing AWS S3 retrieval vs storage costs. Once you have this data, it's up to you to figure out where these resources might be by futzing with different filters in Cost Explorer and drilling things down.

Then there are third-party tools. They fill the gaps left by Cost Explorer and QuickSight and provide heads-up displays which can help you focus in particular areas. Most of them combine Cost Explorer, Budgets, and Trusted Advisor to track spend and wastage across services. Waste is not a trivial thing, and it's likely a significant contributor to your cloud spend, and while reducing waste requires running through a fairly standard checklist (remove idle servers, unused volumes/snapshots/AMIs, stop servers after business hours), it is often ignored until the last minute, and that's how cloud costs balloon. Removing waste is a proactive process, not a reactive ad-hoc exercise, and most third-party tools can help you track it.

When the above solutions don't cut it you need to get your hands dirty. Redash and Athena/Redshift are a deadly combo for drilling-down your spend. I've written some [helpful Athena queries for Cost and Usage Reports]({% post_url 2019-08-29-taming-aws-costs-with-cost-and-usage-reports-and-aws-athena %}). Set up Redash, create some dashboards for daily spend by service, region, and resource tags (these are important). Lastly, track your variable spend and fixed spend separately.

## Is it CapEx or OpEx?

Two key benefits push people towards the cloud: the marvels of on-demand pricing and resource auto-scaling. Basically, we pay for what we use.

Over time, things become more or less fixed, and our cloud expense becomes a hybrid of fixed capital expenses and usage-based operational expense.

Things stabilise and achieve equilibrium. Either our traffic never dips below a specific limit, or our instance types coalesce into c- and m- classes, or the geographical region for our business doesn't change. And then it makes sense to incur a capital expense for a wholesale discount. Reserved Instance pricing and Savings Plans can help reduce costs in return for a 1-year or 3-year commitment, but this also puts a lower limit on how much we can save. Remember, we're committed to paying for Reserved and Savings Plans even if we don't use them.

Commitment-based plans like Reservations and Savings Plans give a discount in exchange for giving up the notion of pay-as-you-go pricing.

Individual services also have CapEx and OpEx components. For example, load-balancers and NAT gateways have fixed hourly rents in addition to data transfer charges. Optimising such hybrid components requires understanding both components. So you might reduce your CapEx by merging multiple load-balancers, in exchange for slightly reduced reliability (your mileage will vary). You may even choose to re-architect, such as replacing load-balancers completely with Route53 round-robin. I tried to do this, unsuccessfully, some years ago. Still, the tools and my understanding of this has improved significantly since then. My point is, understanding pricing models of each service will open up different options.

## Cost optimisation or cost reduction?

One typical strategy when attacking cloud spend is to find the top 3 most expensive items and whittling them down to nothing. Then picking the next 3. Repeat.

This race-to-the-bottom strategy is useful when you're doing ad-hoc cost reduction, and when the only goal is reduction. You may even want to identify the least expensive resources and check if you really need them; if nothing else, it will reduce noise while your team is focused on cost reduction. This plan works well when you're trying to reduce cloud spend on a war-footing. But it is not a practical, scalable, long-term plan and can end up causing a lot of operational anxiety if done too frequently.

Remember the Pareto principle: reducing the initial 80% of the cost requires only 20% of the effort. That initial 80% is pretty standard: reduce waste, remove idle resources, right-size things, purchase Reservations, etc. But after this, you're then left with refactoring individual projects which is very time-consuming.

A more pro-active, inside-out, bottom-up strategy would be to optimise cost against a single business metric: one metric to rule them all. Consider revenue.

Let's say you decide that you're okay with spending 10% of your revenue on cloud expenses. While this number will vary depending on your industry, as some rely on cloud more heavily than others, having this single number is essential. This number means that as long as cloud spending is less than 10% of revenue, no ad-hoc, outside-in, top-down cost-reduction is required, and business can continue as usual. You can also define this cost/revenue percentage for individual teams and assign them a budget based on their revenue contribution. With this model in place, they can drive their cloud spend and optimise their architecture wherever necessary. With project-level insight and a clear target to hit, requirements analysis and architecture discussions can consider the cost factor upfront and keep costs under control. Inside-out, bottom-up.

So cost-optimisation is about cost-efficiency. It matters a lot if a service spends $1 to make $2, or $5, or $10, since optimising those three cases call for different trade-offs around effort, effectiveness, and opportunity cost. Track costs as a percentage of revenue, share hourly, daily, or weekly metrics with teams that need them, and define OKRs to let them drive their spend. Using last month's cost as a yardstick is excellent for reduction, but not for optimisation.

## Every AWS service charges differently

The pay-as-you-go pricing model is great but what you pay for will have a significant impact on your application architecture.

Take RDS, for example. Postgre charges for storage but Aurora charges for both storage and IOPS. To cut down on Postgre costs, you will need to purge data and migrate to a smaller disk. To cut down on Aurora costs, you will need to change your code to reduce database operations. Trade-offs like these will affect your choice of database.

Same for S3. The cost of storing data in S3 is very cheap (though BackBlaze is more affordable). But the cost of retrieving data might balloon out of control, especially if you're using S3 as a data lake. Reducing or optimising S3 storage and operations cost will require different strategies, such as exploring S3 storage classes or building caching layers for your applications.

Every service charges differently and on different metrics, and optimising the cost of each will require understanding its pricing tier.

## Here there be dragons  

There are always a few cost savings items we don't look at, the ones that we postpone for tomorrow. For me, it's data transfer. Every time I go through cloud spend and see data transfer costs rising, my first reaction is to postpone it because tracking it requires a lot of time and energy. It's notoriously hard to account for every byte of data transferred.

There aren't a lot of options available to track data transfer. Some third-party tools are getting close to giving this insight, and they almost exclusively rely on Cost and Usage Report to get this data. You may be able to identify, say, an errant NAT gateway which is costing too much and then use flow logs to track down the transfer further. This kind of exploratory surgery is necessary for analysing data transfer because what you're paying for is the interaction of two different services.

We all have a dragon, some service or resource we ignore because we don't have the time for it right this minute. It's surprising how often we give up on cost-saving opportunities simply because getting visibility into them is so hard. But you can slay your dragon or ignore it until it eats you. Find the area of your cloud you're not optimising and ask why.

## Wrapping up

Reducing or optimising cloud spend is a hard problem to solve. AWS today has over a hundred different services, and many have more than one pricing model. There are a lot of combinations and understanding all of them is the key to getting this beast under control. If that sounds overwhelming, remember that the bottom-up approach ensures that each team only needs to understand the pricing model of their own services. In turn, this will reduce the pressure at the global level to optimise across the organisation.

So:

1. Comprehensive education about our technology stack, as always, is the best way to deal with this problem.
2. Get yourself a cost dashboard. Some problems need the SQL sledgehammer.
3. Understand your fixed costs and your variable costs, and have a plan in place for both.
4. Optmising cost and reducing cost are two very different things. Identify which one you need early on and plan accordingly.
5. Each service is priced on different metrics. These will impact your cost and your application architecture.
6. Find your dragon. Some cost problems are especially thorny. Build the capability for drill-down and alerting on thee costs as early as you can.