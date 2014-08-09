---
layout: post
title: Lambda Behave testing framework
---

Today I attended a small hack day organized with the purpose of trying out and play a little bit with a new Java testing testing framework. The framework is called Lambda Behave, and it is developed and maintained by Richard Warburton, author of the book **Java 8 Lambdas**. More details and the public source code are available at this link: [http://richardwarburton.github.io/lambda-behave/](http://richardwarburton.github.io/lambda-behave/)

One of the goals of the framework is to provide the users a way to express them better in their tests by means of a very convenient fluent interface, that recalled to me the Jasmine framework that I used to write some Javascript tests.
To achieve this, the new Java 8's lambda are used.

I got the chance to play a bit with the latest version installed directly from the sources via maven, while the latest one available from Maven central is the version 0.2.
In particular, I tried it with a little exercise that the organizers suggested. I've created in my GitHub account a small repository containing my solution to the exercise at this link: [https://github.com/theimplementer/lamba-behave-bank-example](https://github.com/theimplementer/lamba-behave-bank-example)

As you can see, it's very easy to write code that reads really well.

The framework is still at an initial version, and today's event was also a chance for the author to gather some suggestions and ideas about improvements, fixes and further developments that would be nice to have implemented.

I personally liked playing with the framework, and I also found very easy to start using it. There's surely room for developments and improvements, and I believe it's a project worth keeping an eye on.
