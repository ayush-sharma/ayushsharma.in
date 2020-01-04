---
layout: post
title:  "I Have Something to Say II"
number: 34
date:   2017-03-20 0:00
categories: general
---

3 days ago we faced some weird issues with our production.

One of our cron machines was facing issues with DNS resolution. It appeared googleapis.com changed their IP address and our machines were having trouble resolving it. We changed the DNS entries in resolve.conf and the problem seemed to abate, but not completely. At the time the cron machine seemed to somehow start doing its job again so we ignored it and moved on.

Then the traffic on one of our load balancers began to fluctuate. It seemed we were having an outage every 3 hours, almost like clockwork. We were also getting a lot of "unauthenticated user" issues with MySQL so it seemed to confirm our suspicions that there was something wrong with DNS entries. Another developer suggested that we were getting hacked and someone was trying to gain access to our MySQL. There were also indications that a recent code release and some changes on the business side might have been causing the issue. The problem symptoms were severe, and consistent.

So we got everyone out of bed, got the dev and product teams involved, and we began a carpet bombing approach reminiscent of that one scene in Apocalypse Now. You know the one I mean. We looked at everything. Code issues, DNS issues, database issues, caching issues, deployment issues. Everyone was looking at everything, and everyone was finding something. And we were trying to fix everything.

The situation was urgent and demanding, so we all tried to be professional and refrained from finger pointing, although somewhere inside I blamed the development team for not doing their job. I also blamed a lot of other people for not informing the Infra team of changes made. We couldn't be expected to handle situations like these if we don't know what's going on, right? Right?

We still manually log in to machines and make changes. Our machines are not all alike. I've heard the philosophy that if you're doing infrastructure at all you're doing it wrong, and I felt at the time we were doing it wrong. I questioned our processes and our place in a world where a lot of progress on servers was being made in the direction of not doing anything on them at all. Serverless and NoOps are new buzzwords, and we seem to be stuck in a century where you still need to log in to machines to configure things. Life was not kind to me for 3 days.

After a lot of carpet bombing, one of our engineers rightly figured out that the new IP address for googleapis.com was conflicting with the routing tables for a new data center I was setting up. It seemed the IP address range I thought was private was not private at all, and was in fact in the public IP space, causing conflicts in resolution. Which in turn caused one of our applications to behave incorrectly, which in turn caused all of the problems.

I should have taken the initial incident with the cron machine more seriously, and should have done a proper RCA. Had I just checked the return IP for the Google domain, I would have noticed that it would have conflicted with the IP range for our new datacenter. I'm reading The Phoenix Project right now, and unprofessional oversights like these are not making me feel very well. The solution was staring me in the face 3 days ago, but it didn't connect with anything, much like the word Yellow didn't connect in Arthur Dent's mind until much later.

I've learnt not to ignore the obvious, and I've learnt that you can waste a lot of people's time over stupid shit if you don't check your assumptions and premises. I hope this lesson sticks with me long enough to become better at my job. Right now, life sucks, and I find myself questioning my value to my organisation and the way I do my work. I need to get better, but it won't happen if I keep taking 1 step forward and 5 steps back. Something needs to change, and change fast.

These have been the confessions of a firefighter who ignored the smoke.