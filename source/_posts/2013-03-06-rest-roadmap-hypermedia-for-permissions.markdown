---
author: jhrmn
comments: true
date: 2013-03-06 14:02:14+00:00
layout: post
slug: rest-roadmap-hypermedia-for-permissions
title: REST Roadmap - API Hypermedia for permissions patterns
wordpress_id: 145
categories:
- Architecture
tags:
- Hypermedia
- REST
- Security
---

It's been a while since my first post in the REST Roadmap series on [RMM Level 2 / verbs / HTTP status codes](http://pragmaticapi.com/2012/12/25/rest-roadmap-upgrading-richardson-maturity-model/). I'd like to continue moving up the [RMM](http://martinfowler.com/articles/richardsonMaturityModel.html) chain, looking at Hypermedia as the Level 3, according to Martin Fowler and Leon Richardson.

![](http://res.cloudinary.com/jhrmn/image/upload/v1362658583/rmm_level_3_zf1hpv.png)

In the spirit of pragmatism, I'd like to tune out a lot of the chatter about media types, link formats, and the like when it comes to hypermedia for the sake of this post. We'll focus completely on the notion of how to use Hypermedia concepts to provide security context to our users without creating an arbitrary security construct.
<!-- more -->
Let's look at a fictionalized set of API URIs/responses, utilizing JSON. We'll assume that we've authenticated to our API with something like Oauth, so we have the security context of the API consumer.

[gist id=5097281]

While this is certainly a boring case, thus far it's semantically sound. However, we really don't know what else we can do with the 'widget' we requested. 

[gist id=5097293]

Now we have a sense of what we're capable of by looking at the 'links' object array. The first link tells me fairly clearly that I can "create a Cog by POSTing to /widget/5/cogs". In addition, I can "List Cogs by GETing /widget/5/cogs". Obviously there are a variety of potential naming conventions here, but the gist is simple: provide a relationship for the related link, what kind of resource that link is, and what verb+URI to get the job done.
Our front-end developers can begin to provide actions/buttons for their users to act upon this "create Cog" link. Not only does the link indicate the capability to complete this action, it also provides the Verb+URI to make it possible. This provides our developers the opportunity to avoid hard-coding that URL, thereby reducing one more hard-coded dependency in their app.

[gist id=5097295]

Note that when we login as a user with less privileges, we no longer have a link for "create Cog". For our front-end developers, this should indicate that they would not provide GUI options for creating this related resource. This should provide a more consistent user experience, where we are not prompting them to take actions which we know will not work for them (thus avoiding trips back to the server for invalid operations/400s/500s).

Obviously links can take many forms. In my experience, the typical relationships are list, item, create, update, delete. In typical pragmatic REST fashion, you should tailor this to your specific needs, while keeping in mind future sustainability.

Hopefully this gives you a start on implementing a Hypermedia strategy in your RESTful API which will give your front-end developers some quick bang for their buck. More poignantly, our application end-users will have the opportunity for a more consistent user experience while utilizing the apps that are fed from our API. Everybody wins ;)

I'm sure this will invite plenty of flamebait for criticisms of the hypermedia implementation noted. I invite your feedback, let's just stay focused on pragmatic approaches, and not get too academic or dogmatically prescriptive, hopefully we can all learn something. I think it's safe to say none of us have this figured out perfectly yet, my hope is this helps folks get the ball rolling, while getting some quick wins.
