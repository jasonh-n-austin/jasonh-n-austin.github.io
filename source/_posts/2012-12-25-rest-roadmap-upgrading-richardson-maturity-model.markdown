---
author: jhrmn
comments: true
date: 2012-12-25 06:30:26+00:00
layout: post
slug: rest-roadmap-upgrading-richardson-maturity-model
title: REST Roadmap - Upgrading in the Richardson Maturity Model
wordpress_id: 5
categories:
- Architecture
tags:
- HTTP
- REST
---

## REST Roadmap Series


I'd like to kick off my new blog with a series I'm calling, "REST Roadmap". The idea is to put together a series of basic changes to the typical API which will ensure future success. The first installment is around getting a feel for what REST really is via the Richardson Maturity Model. In my experience, most organizations have made some progress, intentionally or inadvertently, in this regard, and are starting at around Level 1 of RMM.
<!-- more -->


## Background on RMM (the Richardson Maturity Model)


In the software industry (namely the Free Software Movement), the phrase "standing on the shoulders of giants" is oft used. When we investigate REST, proverbial technology giants like [Roy Fielding](http://en.wikipedia.org/wiki/Roy_Fielding), [Leonard Richardson](http://www.crummy.com/self/), and [Martin Fowler](http://en.wikipedia.org/wiki/Martin_Fowler) come to mind. Fowler worked directly with Leonard Richardson, Jim Webber and others to put together some guidelines on how to architecturally construct a REST API. A wonderful culmination of their respective genius is in Martin Fowler's article titled [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html).

![](http://res.cloudinary.com/jhrmn/image/upload/v1362658921/rmm_alxpgg.png)

_(Image credit: Martin Fowler)_

The nearly oversimplified concept in this article is that a RESTful API can be built in a series of building block-style steps. In practice, there are many gritty details that occur naturally in traversing these steps.

In my experience, reaching the first level of the RMM is fairly common 'in the wild'. Anybody who wants to follow some sort of modern notion of REST tries not to build resources which "smell like SOAP". Martin Fowler refers to this style as "The Swamp of POX", aka "Plain old XML". Desiging resource URIs which try to represent the CRUD style of 'objects' in the domain is becoming more common.

However, CRUD-driven objects as resource URIs is really only the beginning. The RMM diagram in Martin Fowler's document suggests we are really only halfway there at RMM Level 1.


## Your API is not really RESTful


As you start to read more deeply into the world of REST, you will find that according to many experts in the field, this is the most common starting point. While everyone wants to discuss their "REST API", until you have reached fully agile Hypermedia status, you are really not meeting up to the standards of [Roy Fielding's 2001 dissertation on REST](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm).

Once organizations move past "the Swamp of POX", they move into using resource-oriented, CRUD-based URIs. This is an important step in the evolution of an API, but certainly should not be looked at as a stopping point.

Many of the common misconceptions about what a REST API is comprised of are well summarized in Roy Fielding's article dubbed [REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven).



## HTTP Verbs - RMM Level 1 => 2


We've recently been working on mobile applications, moving toward utilizing HTTP status and verbs to drive action, versus binding that workflow up in application-specific JSON. As much as error/exception handling can be an afterthought in the average development cycle, it has to be a first-class citizen in an API driven world. As part of Layer 7's "API Academy" series, they did a great job at describing the 'rights' and 'wrongs' in the video titled [Handle Errors on the Web](http://www.youtube.com/watch?v=NTObb3ZS1nk).

I put together a some materials to help walk our mobile and API teams through the steps we were about to take on this journey up the RMM ladder. I've since amended the presentation with cleaned up audio, thus upgrading it to a screencast!

http://www.youtube.com/watch?v=7ue3aMk4g2I



## HTTP Status Codes


The most important thing to note is that while the RMM description talks about implementing verbs as the heart of RMM Level 2, this is an empty effort without effective communication of error statuses. HTTP Status Codes should serve to provide you with low-level, protocol-grade feedback on the state of any resource/verb you are working with. Digging into applicaiton specific messaging should only really provide you with application-specific, and/or user-specific details which allow you to address the error/exception.

The ability to quickly recognize that an API call is not going to answer with "OK" is the first priority in any interaction, providing you with a responsive UI, regardless of mobile/web/whatever client. This quick protocol-level feedback also provides scaling advantages. If I'm creating or updating a resource, there is no need to reverberate back what was provided to the server. 201/Created or 204/No Content are often the best appropriate response to indicate that the operation requested was not errant. More importantly the lack of body content means that wire traffic is non-existent past the headers.



## Summary and What's Next



In my next article on BDD and REST, I'll address some of the difficulties in testing APIs that only answer in 200 & 500, or OK and Oh $h1t! Looking beyond the basic CRUD functionality of a resource, and trying to envision the Hypermedia-driven behavior of a future resource/revisions is well aided by BDD/ATDD strategies.
