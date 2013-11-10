---
author: jhrmn
comments: true
date: 2013-01-21 01:30:27+00:00
layout: post
slug: bdd-atdd-for-your-agile-rest-api-part-2
title: BDD / ATDD for your Agile REST API, part 2
wordpress_id: 81
categories:
- Testing Automation
tags:
- ATDD
- BDD
- REST
---

Part 1: [http://pragmaticapi.com/2013/01/12/bdd-atdd-for-your-agile-rest-api/](http://pragmaticapi.com/2013/01/12/bdd-atdd-for-your-agile-rest-api/)

I recently covered how BDD principles can provide a great touch stone for acceptance testing in REST-based API platforms. In this brief followup, I'll show how the Cucumber-JVM framework provides all of the same functionality that we saw in .NET's SpecFlow, using the Groovy programming language.

<!-- more -->

Code for this example can be found on [Github](https://github.com/jasonh-n-austin/TwitterRestTestsGroovy). This should provide you with a working example of BDD testing the Twitter API using Cucumber-JVM, Gradle, and Groovy.

Much like the CLR in .NET, we can use the JVM to provide access to a variety of programming languages. JUnit tests can be developed with Cucumber-JVM, looking almost just like the NUnit-based SpecFlow tests in Part 1. However, this example will show how JUnit, and thereby, any unit testing framework, doesn't necessarily need to be utilized.

Using groovy command-line based invocation of Cucumber tests can be very useful in scenarios where there is interoperability with other Groovy-based tools, namely SoapUI (commonly used in QA web services testing). It is easily possible to create classes which encapsulate the logic necessary to test your API, providing hooks for your Cucumber tests alongside SoapUI, or any other Groovy-based tools.

It is easily arguable that Groovy is well suited for this task. Groovy has some great features like the built in JsonSlurper, great library support (such as the excellent utility [RESTClient](http://groovy.codehaus.org/modules/http-builder/doc/rest.html) library), and dynamic ability to walk JSON & XML data.

As a review, here is our feature file, for testing a basic portion of the public Twitter API. This time around, we've added a few small differences, in the treatment of querystring parameters. This is meant show that there are various ways to describe your tests, not simply monolithic URLs.

{% gist 4582935 %}

This maps to steps in our RestTests.groovy file, which utilizes the RESTClient class to manage our GET. There are plenty of options out there for a web driver, RESTClient happens to be fairly well suited for our purposes.

{% gist 4536191 %}

Most of this code is fairly self-explanatory. For those unexposed to the wonders of Groovy JSON handling (and XML as well), the function starting with "it should have the field" provides some interesting syntax:

{% gist 4582955 %}

The 'parsed' variable holds the parsed JSON data. As we have extracted the 'field' variable , we can use a closure to dynamically evaluate whether that field exists in the results.

When we run **_"gradle --quiet cucumber"_**, we see the following output.

![](http://res.cloudinary.com/jhrmn/image/upload/v1362658828/groovy_cucumber_twitter_lpg6yj.png)





Overall, compared to either Java or C#, we end up with some very terse code using Groovy. While the difference is not stark with this very simple example, the delta will start to widen as the complexity increases, especially when complex response validation comes into play. In addition, Groovy has some great built-in libraries for building up JSON data, which can be very useful when testing PUT & POST scenarios.

While the Gherkin syntax that SpecFlow and Cucumber-JVM share give us a fundamental link, the BDD premises in defining steps in plain English are really the most powerful aspect, regardless of technology. RESTful API testing can be very daunting with many of the commercial and open source tools available; it is my opinion that BDD/Cucumber based testing is a natural fit. Hopefully you'll find the same to be true after working with the examples in this article and the preceding article.

Happy testing!
