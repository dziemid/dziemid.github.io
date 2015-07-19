---
layout: post
title: "Delivery engineering team"
date: 2015-07-19 10:45:59 +1000
comments: true
categories: agile devops
---

## What is it?

One definition could be:

> Delivery engineering team enables others to deliver business value faster.

Others in this context relates to product/project software delivery teams.

<img itemprop="image" src="/images/deleng.jpg"/>

It seems to be fairly new term, judging by lack of definition in google or recent tweet from #devopsdays in Melbourne:

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">Finally an appropriate name for what i actually do - Delivery Engingeering. Thanks <a href="https://twitter.com/hashtag/devopsdays?src=hash">#devopsdays</a></p>&mdash; griggle (@griggle) <a href="https://twitter.com/griggle/status/621483550974439425">July 16, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Other, maybe more common name seems to be ["tooling team"](https://www.rallydev.com/blog/engineering/you-don-t-need-devops-team-you-need-tools-team).

It's definitely not a ["devops team"](http://www.thoughtworks.com/radar/techniques/separate-devops-team).

## What's in?

In the delivery engineering team I am part of, some of the metrics we are trying to improve:

* CI Build time
* Deploy time
* New server creation time
* New production like environment creation time
* New developer laptop setup time

I guess there are many ways to go about moving above, but in the teams I've worked with, the day to day often was:

* Moving software from legacy platform to a new one. (Think AWS/docker migration)
* Creating tools and practices to support above.
* Technology evangelism, training & support for product teams.

## What's out?

The product teams should have full freedom and responsibility to run thier apps in production.

So it's not responsibility of delivery engineering team to

* Upgrade frameworks and libraries used by product team. The product team itself should do it.
* Carry a pager, be on-call as support for production systems (unless those are the systems that delivery engineering team is a custodian of, for example CI server)
* Be a gate keeper on a path to production. The product teams themselves can decide what and when to release.

## Techniques & Practices

Some of the techniques and practices important for delivery engineering team

* Team rotation
* [Continuous Delivery](http://martinfowler.com/bliki/ContinuousDelivery.html)
* Showcase


## Do you need delivery engineering team?

Maybe :-)

You most likely need delivery engineering, and if you are big enough, you may decide to form a team around it. The size I have an experience with and seems to work OK, is one small delivery engineering team for roughly three product teams. To use [spotify terminology](http://blog.crisp.se/2012/11/14/henrikkniberg/scaling-agile-at-spotify), delivery engineering team is part of tribe and supports squads.

## To sum up

This is based on my experience, what do you think? :-)

