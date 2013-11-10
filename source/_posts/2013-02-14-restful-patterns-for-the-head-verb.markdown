---
author: jhrmn
comments: true
date: 2013-02-14 05:25:05+00:00
layout: post
slug: restful-patterns-for-the-head-verb
title: RESTful patterns for the HEAD verb
wordpress_id: 91
categories:
- Architecture
tags:
- HEAD
- HTTP
- REST
- RMM
---

In the typical usage of HTTP, the GET and POST verbs seem to get the most mileage. I've previously covered some aspects of moving up the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html) scale. Implementing the PUT and DELETE verbs is typically a step up to RMM Level 2. There are other HTTP verbs, outside of the 'by the book' RESTful patterns, which can prove very useful in certain situations. One of the easiest to implement verbs, with some great benefits in scaling terms, is the HEAD verb.

![](http://res.cloudinary.com/jhrmn/image/upload/v1362658838/rest_head_tpp1jm.jpg)

<!-- more -->

If you're not familiar with HTTP HEAD, it's best to start with the specifications at [http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)


### 9.4 HEAD




> _The HEAD method is identical to GET except that the server MUST NOT return a message-body in the response. The metainformation contained in the HTTP headers in response to a HEAD request SHOULD be identical to the information sent in response to a GET request. This method can be used for obtaining metainformation about the entity implied by the request without transferring the entity-body itself. This method is often used for testing hypertext links for validity, accessibility, and recent modification._

_The response to a HEAD request MAY be cacheable in the sense that the information contained in the response MAY be used to update a previously cached entity from that resource. If the new field values indicate that the cached entity differs from the current entity (as would be indicated by a change in Content-Length, Content-MD5, ETag or Last-Modified), then the cache MUST treat the cache entry as stale._


Simply put, HEAD returns all of the HTTP headers, just like GET, but provides no body content. All of the same rules regarding content caching can be applied, regarding headers in the request/response.

There are a few scenarios where HEAD is exceptionally useful:

**Existence checks:** 

In cases where the content of a resource is not particularly important, but the existence of the resource is, HEAD is perfect. We can do everything just like a GET and check the response code, without the weight of the response body.

As an example, we'll look at Twitter's public profile API.

    
    GET http://api.twitter.com/1/users/show.json?screen_name=jasonh_n_austin


The server responds:

    
    200 OK
    {
    "id": 57005215,
    "id_str": "57005215",
    "name": "Jason Harmon",
    "screen_name": "jasonh_n_austin",
    "location": "Austin, TX",
    "url": "http://pragmaticapi.com",
    <snip>





	
  * Cache checks:


When we call this using GET, there is a definite weight to the response body...enough that I'll snip it for sake of brevity. If we wanted to simply validate that a profile exists, we could use HEAD and simply evaluate the HTTP status (as there will be no response body):

    
    HEAD http://api.twitter.com/1/users/show.json?screen_name=jasonh_n_austin


The server responds:

    
    200 OK


That's it! In cases where the profile doesn't exist, the response code will be a 404 (much like you've seen when you request a non-existent web page).

    
    HEAD http://api.twitter.com/1/users/show.json?screen_name=jasonh_n_portland


The server responds:

    
    404 OK


There's really no simpler way to determine if a resource exists in an API. This is very nice when there is a secured resource (such as a username or email of some user) which you cannot expose the details of. HEAD prevents any potential information being exposed, other than the existence of that item.

**Cache check**

When the resource in use supports caching, HEAD can be useful to check if there is a new version of the resource you have previously retrieved. When you are dealing with distributed scenarios in which data is potentially cached for periods of time (such as with a mobile device), it can be advantageous to use HEAD to investigate the previously retrieved resources. If there has been a change, the 200 status code indicates there has been an update; otherwise 304 indicates the data is the same as when you retrieved it last.

A well-implemented example of this in a public API is at Github. They implement two models of cache validation in the response headers:

1. ETag: this is typically a calculated hash on the output object, expressed in the "ETag" response header.



	
  * In the next request, the If-None-Match request header should utilized the ETag as previously supplied in the response.


2. Last-Modified: normally a database-or-otherwise-maintained "last modified date" is maintained for the resource, which is supplied in the "Last-Modified" response header.



	
  * In the subsequent request, the If-Modified-Since request header can be populated with the previously retrieved 'last modified-date'.


In either case, the results are simple; 200 means there is new content, 304 means nothing has changed.

While HEAD can be useful for certain caching situations, most folks elect to simply use GET here, as the 304 will not supply a body, and the 200 will reply with the new content in the body. This saves the extra hit for HEAD+200 as well as GET+200+Content. However if you have one process which looks for updates, and another which does the work of retrieving the new content, this model can be very helpful in reducing traffic over the wires.

Example request:

    
    curl -i https://api.github.com/users/jasonh-n-austin
    HTTP/1.1 200 OK
    Server: GitHub.com
    Date: Thu, 14 Feb 2013 04:52:06 GMT
    Content-Type: application/json; charset=utf-8
    Connection: keep-alive
    Status: 200 OK
    X-RateLimit-Limit: 60
    X-RateLimit-Remaining: 50
    Vary: Accept
    Cache-Control: public, max-age=60, s-maxage=60
    Last-Modified: Tue, 12 Feb 2013 02:58:46 GMT
    ETag: "f3effd3e870e79f8aed863cf8c8b319f"
    X-GitHub-Media-Type: github.beta
    X-Content-Type-Options: nosniff
    Content-Length: 1482
    
    {
    "login": "jasonh-n-austin",
    "id": 1305160,
    "avatar_url": "https://secure.gravatar.com/avatar/366e53efc68d5899edc95b2c4c9c2619?d=https://a248.e.akamai.net/assets.github.com%2Fimages%2Fgravatars%2Fgravatar-user-420.png",
    <>


Now that we have content, let's check to see if it has changed:

    
    curl --head -i https://api.github.com/users/jasonh-n-austin -H "If-Modified-Since: Tue, 12 Feb 2013 02:58:46 GMT"
    HTTP/1.1 304 Not Modified
    Server: GitHub.com
    Date: Thu, 14 Feb 2013 04:52:32 GMT
    Connection: keep-alive
    Status: 304 Not Modified
    X-RateLimit-Limit: 60
    X-RateLimit-Remaining: 50
    Vary: Accept
    Cache-Control: public, max-age=60, s-maxage=60
    Last-Modified: Tue, 12 Feb 2013 02:58:46 GMT
    X-Content-Type-Options: nosniff


Note that the If-Modified-Since request header matches the value in the original Last-Modified response header.



**Summary**

In REST talk, we usually refer to the four verbs, GET/PUT/POST/DELETE, as though that's all there is to HTTP. The HEAD verb reminds us that we have more tools in our bag, especially when wire traffic, data access costs, and potentially large response bodies are a big concern. Implementing HEAD is typically fairly simple in most RESTful application frameworks, and can pay real dividends in terms of scaling a platform, when used wisely.

Good luck!
