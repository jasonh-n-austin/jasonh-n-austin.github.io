---
author: jhrmn
comments: true
date: 2013-01-12 03:36:31+00:00
layout: post
slug: bdd-atdd-for-your-agile-rest-api
title: BDD / ATDD for your Agile REST API
wordpress_id: 21
categories:
- Testing Automation
tags:
- BDD
- Behavioral Driven Development
- REST
---

In any system architecture, a multi-tiered testing strategy is critical. Much of this work is typically behind the scenes in development teams, often following TDD-oriented practices. In agile terms, stakeholders and product owners often never see or grasp what's being tested. Within the unit testing and integration testing tiers, this tends to make sense. However [Behavioral Driven Development](http://en.wikipedia.org/wiki/Behavior-driven_development) (aka Acceptance Test Driven Development) begins with acceptance criteria definition and finishes with the acceptance of a story, providing engagement for all members of an agile team from start to finish.
<!-- more -->

_NOTE: All code from this post is on github: [https://github.com/jasonh-n-austin/TwitterRestTests](https://github.com/jasonh-n-austin/TwitterRestTests)._

In the world of defining acceptance criteria for API development, this approach can be very useful. For those of us on dedicated API teams who have tried to utilize Agile demos using SoapUI or the like, we know the cold dead eyes of bored stakeholders and product owners. It's hard to think about adding value to your business domain when we're all staring at URLs, headers, and status codes. However, the alternative of never showing anyone in the business how your API works certainly loses a depth of understanding that is requisite in today's web platforms.

In some cases, a pervasive culture of [TDD/Test-Driven Development](http://en.wikipedia.org/wiki/Test-driven_development) can provide the only practice providing cohesion between developers and business. If anything, business folks might understand terms like "code coverage" or "test case", but really have to trust that the developer has built adequate testing when they accept the quality of new features.

Behavioral Driven Development approaches acceptance testing by using plain english expression as the interface for the tests. This approach lends itself well to explaining the behavior of your RESTful resource in Domain Specific Language, i.e. your own business lingo. Here's an example of what a typical scenario definition might look like for a REST test:

[gist id=4521824]

The above example (functional in the [Github](https://github.com/jasonh-n-austin/TwitterRestTests/blob/master/TwitterRestTests/User.feature) project) provides a rudimentary GET test on Twitter's public, no authorization required API. Obviously in authenticated scenarios this would look a bit more complex.

These semantics are fairly easy to understand, and provide some means to abstract away the complexities of testing. In addition, if there are standards within the API platform that need to be verified, that can be addressed within underlying testing code, without re-establishing all of the standards in every test.

Note the tags (delineated by the "@" symbol). This provides a means to provide some logical groupings by which to call various resources+verbs.

The 'step definitions' form the code behind the 'feature' file you see above. An example of the step "I retrieve the results" follows:

[gist id=4521814]

The attribute 'When' defines the regular expression match which matches steps in the feature files. In this case, a simplistic use of the WebClient class provides us with a means to interact with the REST API. Once the interactions have taken place, assertions can take place based on status code, initially:

` [gist id=4521797]`

Using JSON.NET, we can parse the result content and verify specific fields and values:

`[gist id=4521805]`

Of course, these concepts can be taken further to test more details about all aspects of a RESTful API. Of course, the complexity of POST & PUT are going to be more complex and will take some forethought on the approach. Hopefully this will get your thought process starting on how the BDD approach will fit into your acceptance testing strategies for your REST API.

Once you have a test suite developed, and your team engaged in defining BDD-based acceptance criteria and running tests, you should immediately look to get integrated into a [Continuous Integration](http://en.wikipedia.org/wiki/Continuous_integration) server such as TeamCity or Jenkins.

_ _

_All samples utilize , a .NET-based BDD framework. These same concepts would work equally well with [Cucumber](http://cukes.info/), [Cucumber-JVM](https://github.com/cucumber/cucumber-jvm), or any [Gherkin](https://github.com/cucumber/cucumber/wiki/Gherkin) - based framework._

For SpecFlow installation in VS2012, please refer to the documentation: [https://github.com/techtalk/SpecFlow/wiki/Install-IDE-Installation](https://github.com/techtalk/SpecFlow/wiki/Install-IDE-Installation)
