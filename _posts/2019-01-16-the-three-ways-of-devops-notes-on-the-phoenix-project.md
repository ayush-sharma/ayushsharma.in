---
layout: post
title:  "The Three Ways of DevOps: Notes on The Phoenix Project"
number: 77
date:   2019-01-16 0:00
categories: books
---
Bill Palmer, the Director of Midrange Technology Operations at Parts Unlimited, has a problem.

After years of looking after a small team responsible for providing reliable network operations for his company, Parts Unlimited, Bill is yanked out of his comfort zone and asked to take over as the VP of IT Operations for his organisation. But he is understandably hesitant. Two of his superiors have just been fired, continuing in a long line of revolving door executives who would come in, implement new systems, figure out where the bathrooms are, and leave before things got bad. Bill hesitantly accepts the position, only to find out that things are a lot worse.

The main product of the company, The Phoenix Project, is stuck in the mud. The competition keeps leap-frogging them with exciting features. They are losing revenue and market-share, and cannot innovate fast enough to keep up the pace. Their main star developer Brent has lodged himself in every IT process imaginable, and has become a single-point-of-failure as a result. The rest of the IT teams have become inward-looking over the years, and progress is defined by one team finishing their task and throwing it over the proverbial silo to another team, without much in the way of a heads-up. There is no preventive maintenance, there is a lot of fire-fighting, and projects keep getting delayed because people are doing busywork but not moving forward.

Does any of this sound familiar?

<em>The Phoenix Project</em> is a book on DevOps thinking penned by Gene Kim, Kevin Behr, and George Spafford. It's an IT novel about the protagonist, Bill Palmer, a newly minted of the VP of IT Operations, and how he struggles to find a work-life balance when the very definition of work in his organisation has become fungible. As he fights to get some situational awareness of the IT systems in his organisation, with outages and issues to deter him from this goal, it seems for a while that Bill may not be able to get his head above water.

Along the way, he meets a Yoda-like character named Erik Reid, who persuades Bill to look at IT the way a plant manufactures physical goods. By changing his perspective, Bill is able to define and concretise what “work” actually means, and focuses on implementing The Three Ways to better organise himself and his team.

# What Is Work?
One of the central themes in the book is to encourage the reader to think about what kinds of work they do, and to cateogrise them business projects, internal IT projects, changes, and unplanned work. Pushed by Erik Reid, Bill is able to define his work along these lines, and gain a newer understanding of where his team spends most of their time.

## Business Projects
These are initiatives that directly add value to the business, such as adding new features to increase growth or capture market share. This “work” comes as a result of the organisation defining its objectives and thinking about what it takes to accomplish those objectives.

## Internal IT Projects
These projects are those which are needed to either support or enhance the business projects mentioned above, such as internal management dashboard.

## Changes
Changes can be projects in and of themselves, and are generated as a result of working on the two kinds of work above, that is business projects and internal IT projects.

## Unplanned Work
While the 3 kinds of work above are types of planned work, there exists a fourth category of work called unplanned work. This work results due to operational incidents and issues, or due to the unforeseen or unplanned consequences of the first 3 kinds of work. Working on unplanned work comes at the cost of working on planned work, and is sometimes referred to as “anti-work”, because it prevents us from working on meaningful tasks.

Bill realises that visualising these 4 types of work, and tracking them all in a single change control board, is critical to understanding the flow of work and the value-chain. Without this, not all work can be planned or accounted for, which creates scheduling and resource-planning problems, which is what Bill was struggling against in his journey.

Once the types of work have been identified, they can be minimised and mitigated by following The Three Ways.

# The Three Ways

## The First Way
The First Way is about understanding the left-to-right flow of work from dev to ops by gaining a fuller appreciation for the system, not just the individual parts. It encourages maximising the flow of work from left-to-right by reducing batch sizes and intervals of work, so that the “work in progress” is reduced as much as possible. Practices for the First Way include continuous integration, build, and deployment pipelines, as well as being able to create infrastructure environments on-demand, limiting the work-in-progress, and building systems that are safe to change.

## The Second Way
The Second Way is about maximising the flow of feedback from right-to-left at all stages of the value stream, enabling faster error detection and resolution, and ensuring that problems are not passed on downstream. This enables creating quality at the source, and embedding the required knowledge where it is needed. Practices for the Second Way can include stopping the production line when defected are found, creating automated test suites to catch defects early, creating shared goals and shared pain between development and operations, and having pervasive monitoring at every level so that everyone can see the code and environments are operations as designed, and that the customer goals are being met.

## The Third Way
The Third Way is about understanding that repetition and practice are the pre-requisites to mastery. By encouraging a culture of continuous experimentation, risk-taking, and learning from successes and failures, a culture of trust and confidence can be created that elevates our daily work so that we are constantly improving ourselves and the systems we build.

Identifying the kinds of work that we do and implementing the Three Ways to elevate how we engage with our work is the key takeaway of this book. I wish I'd read it years ago, because it crystallises so many of the problems I've faced along the way. It’s not possible for me to do justice to a 300-page book in a tiny blog post, but I’m hoping you see enough wisdom in The Three Ways and how they apply to you that you pick up the book and see for yourself.

A lot of what the book says has been said before, and there will be some out there who will not see anything new, but for someone self-taught like me who aims to find fonts of knowledge to make his work and life easier, this book was amazing in explaining not only which systems might be needed to alleviate certain problems, but also to make clear why those systems are needed in the first place, and what we risk by ignoring them.

One of the criticisms of the book is that by presenting the above story in a fictional setting, it somehow undevalues its message. But I find the story-driven approach emphasises the message rather than undervalue it, and I encourage you to make your own assessment after reading it. I've already bought the next book in the series, The DevOps Handbook, and hope to have another enlightening experience.

## Resources

- [The Phoenix Project on Goodreads](https://www.goodreads.com/book/show/17255186-the-phoenix-project).
- [The DevOps Handbook on Goodreads](https://www.goodreads.com/book/show/26083308-the-devops-handbook).