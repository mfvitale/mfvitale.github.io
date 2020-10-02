---
layout: post
title:  "Are you testing your tests?"
date:   2020-10-06 16:42:38 +0200
categories: blog
---
How many of you heard at least one time in their career this sentence: "**We must have a code coverage >= 80% for release to production**"?

<p align="center">
<img src="https://memegenerator.net/img/instances/54739072.jpg" alt="Code coverage is coming" width="50%"/>
</p>

I totally agree with this sentence

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Code coverage is a side effect, not a goal. The only time I find the metric useful is when I’m working with legacy and want to have an indication if there are tests for the part of the code I’m interested in.</p>&mdash; Sandro Mancuso (@sandromancuso) <a href="https://twitter.com/sandromancuso/status/1091701221390516224?ref_src=twsrc%5Etfw">February 2, 2019</a></blockquote>

I think that sometime (lot of times) this "number" is used only as a goal but it should be a "tool" for developers to understand the fragility of the code that they are going to change. 

* High coverage :relaxed:
* Low coverage :fearful:

So how I can say that my code has a good quality? Certainly doing tests and so having a good coverage but..this is not sufficient. Who tests the quality of your test?!

This is where *mutation tests* come in handy.

## What is mutation testing?
<p align="center">
<img src="https://media.nature.com/lw800/magazine-assets/d41586-019-03536-x/d41586-019-03536-x_17373716.jpg" alt="Mutation" width="50%"/>
</p>

Mutation tests are new type of software testing with the aim to test the quality of your test! They works changing some code and see if there are some tests that fails.
I see in this type of tests an analogy with [chaos enginering](https://principlesofchaos.org/) 

> Chaos engineering is the discipline of experimenting on a software system in production in order to build confidence in the system's capability to withstand turbulent and unexpected conditions.

### Basic concept
Mutation is a change automatically seeded into your code that can be *killed* if your tests fails, or **lived** if your tests pass!
The quality of your tests can be gauged from the percentage of mutations killed.

Very simple!

### Coverage vs Mutation

### PIT, Java mutation tests library

#### Example
show an example and the report


Link to repo

If you put togher high code coverage and a good resilience to mutation test you have a great measure of your code quality!
