---
author: jhrmn
comments: true
date: 2013-03-25 17:21:40+00:00
layout: post
slug: extend-your-crud-http-api-with-sub-resources
title: Extend your CRUD HTTP API with sub-resources
wordpress_id: 183
categories:
- Architecture
tags:
- API
- CRUD
- HTTP
- REST
- Sub-resource
---

When it's time to build your web API, there's often a tendency to build everything around data entities. [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete)-style APIs use GET/POST/PUT/DELETE to provide the same feel as SELECT/INSERT/UPDATE/DELETE in a traditional relational database. 




Once you work through these CRUD-based web API patterns for a while, you will find that it doesn't always match up perfectly to your business needs. I'd like to introduce a design pattern which will add some flexibility to your CRUD-stye HTTP API, while maintaining clean semantics and easy usability. 
<!-- more -->




There are a number of frameworks with actually revolve around creating this clear linkage between a relational database and the 'four verbs' of a typical HTTP API. While the CRUD approach to logical entities will often provide the bulk of an APIs functionality, there are other forms which can be very effective.




Simply put, we can provide an always-existent sub-resource as an extension to your entity-based resource. 
![](http://res.cloudinary.com/jhrmn/image/upload/v1364231618/memecenter_1364231463618_397_eft398.gif)



Let's look at a newly created fictional user account. 

  
_NOTE: Authentication is assumed in the following examples, for brevity._

[gist id=5234609]



It is often the case that we want to maintain a different level of control around a user account than the user's profile. The details of this account might not be made available for the user to change. In our fictional user, the access level/user type is 'read-only'. If I tried to update this user, I might not be permitted.

[gist id=5234612]




However, we might want to provide this user with an update-able profile, that other users can view. When we first touch the profile on a newly created account, it might not be populated in the backend. However, we want to provide the notion that a user profile always exists for a user account, even if they haven't yet updated it. In some cases, there might be calculated fields or other pre-populated data when we first access the sub-resource.

[gist id=5234615]



Note that the naming on this URI is singular, 'profile', not 'profiles'. While I recommend pluralized naming convention for CRUD-style resources (notice 'users'), a sub-resource like this will never be plural. There is a flat 1-to-1 relationship between the selected user and the profile. 



Now that we've recognized the user's profile is basically empty, we can PUT some data to update it.

[gist id=5234620]




Note that if the update was successful, we reply with a 204; no need to provide any more context than that. Now we can retrieve this sub-resource, and see that it matches what we provided.

[gist id=5234622]

Next time you have a critical resource you need to extend, be sure to think about this flat sub-resource pattern. You'll keep the complexity of your content from growing out of control, and avoid mixed security constraints in one operation.

