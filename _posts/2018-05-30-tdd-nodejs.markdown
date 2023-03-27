---
layout: post
title: "Unit Test NodeJS"
date: 2018-05-30
categories:
  - dev
description:
---
Testing is an important aspect of software development. Testing helps to improve the code quality and reduce bugs introduced into the system.


There are many types of tests that can be performed on a software, but the one that developers need to deal with most of the times is unit testing .

Unit tests are often written by developers to test their own code. This type of test targets very small amount of code to test such as a functionin isolation for each test.

Test Driven Development (TTD) was introduced to enforce strict testing process during development. TDD is summarized nicely by [David Tang](https://thejsguy.com/)
 as:

>1. Write a test and watch it fail
>2. Write the minimal amount of code to make that test pass
>3. Repeat

Two parts article on [codementor](https://www.codementor.io/) about TDD, unit testing with NodeJS can be found here:
[Part 1](https://www.codementor.io/davidtang/unit-testing-and-tdd-in-node-js-part-1-8t714s877)
[Part 2](https://www.codementor.io/nodejs/tutorial/unit-testing-tdd-node-js-nockjs-part-2)

The articles covered the useful tools to support TDD and unit tests such as Mocha, Chai, and Nock.
