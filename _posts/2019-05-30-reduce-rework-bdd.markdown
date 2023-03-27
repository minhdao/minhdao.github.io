---
layout: post
title: "Reduce rework with Behavior Driven Development"
date: 2019-03-24
categories:
  - dev
  - thoughts
description:
---
Feeling tired of redoing a same task many times? This post might help you.


## A rememberable Friday

It was a Friday morning and the end of a sprint too. I had already submitted my code for the second task the night before for review. Everything was chill and good. It seemed to be one of those productive weeks and I could "R.I.P" in the weekend.

It was about time for lunch when I recieved an urgent request from our front-end team to change a few things on the first task that I had already finished earlier on Tuesday. To my surprise, I asked myself "What could possibly go wrong?".

The change request was to fix the format of the respond from the API I created earlier. Our front-end team could to parse the repond with out throwing some exception because some expected and required attributes had not been included in the respond. After some times trying to figure out why my respond could not be parsed, I learnt that `id` of the respond was expected to be UUID, and a `meta` object with `total_pages` attributes was also required in the respond.

"Why did't you have these attributes correctly in the first place?" you might ask.

When I started working on my API, I looked at the respond from a few other APIs. The respond included `id` in the form of the UUID, and every respond has `meta` attributes which included `total_pages` used for pagination. The purpose the API I was working on was different from the previous APIs though, so I thought to myself, the respond could be different. Since this time our server would generate the `id`s, so there was no point using UUID. The data could always be returned in one page, so pagination could be removed.

With these thoughts in mind I could only see the pros they bring:

  - Table performance improvement since UUID was out of the picture
  - Smaller code base with less complication and easier to maintain

The only cons that I just discovered was:

  - Our front-end team could not use the API :)

At this point I realized that the rest of the day was not going to be good because I had two unfinished task hanging over my head and only a few working hours left before weekend officially started. The stress and anxiety began to creep in. Everything turned from good to bad so quickly.

After a few hours of stress and help from my colleagues, I managed to fix the respond to fit our front-end team requirements. This time though, I did not forget to cross-check with them before submitting my code and calling it done with the task. However, the API could not finish on time to be deployed, so the customer could not use this feature maybe until the end of next week.

The task that I finished so early in the sprint came back and bit me on the leg. What I thought was a productive week turned out not so productive after all. Rework was definitely the silver bullet that kills productivity and I couldn't help but wonder how to improve my workflow to stop the rework.

Search for a few minutes on the internet, I came accross an article about Behavior Driven Development a.k.a BDD. BDD was no stranger to me though. It was tought in one of the classes I took back in university, but ... without any actual context. In order words, it didn't make much sense to me back then. Having re-reading BDD after all the task rework I went through, the ideas and concepts of BDD finally make much more sense.

## Behavior Driven Development a.k.a BDD

[cucumber.io](https://cucumber.io/) has excellent resources on BDD. This section of the blog will be a summary of the info I managed to collect mainly on cucumber.io and some other articles.

### BDD overview

Behavior driven development is a set of pratices that aim to reduce wasteful activities in software development.

The most common wasteful activities are:

  * Rework caused by misunderstood or vague requirements
  * Technical debt caused by reluctance to refactor code
  * Slow feedback cycles caused by silos and hand-overs

If you already read through the ealier part of the blog, you might realize by now, the causes of my task rework were all among these wasteful activities :)

BBD aims to reduce these watseful activities by introducing practices that will narrow communication gaps, proster better understanding of the customers' needs, and promote continuous communication. All of these are done around **real word examples**.

BDD can be splited into two main parts:

  * Deliberate discovery
  * Test driven development

### Deliberate discovery

A common reason for task rework is misunderstanding of how the feature should behave and what's the problem we are trying to solve. We often assume how the feature should work and think in just our point of view. Because of this we only see our part in the play and how we want the feature to work. It's easy to forget about other people and teams that also be work on the same feature. The worst part is we might also leave the customer, the one that actually uses the feature, completely out of the picture.

According to cucumber.io, it's shown that teams which deliberately seek to discover what they are not clear about before starting development are more productive because there's less rework. The most effective way to deliberately discover the 'unknowns' and 'unclears' is through effective converstaion between the stakeholders.

cucumber.io has a simple yet effective technique to help clearing out the ambiguity of requirements/stories called [**Examples Mapping**](https://cucumber.io/blog/example-mapping-introduction/). It's just a short meeting (25 minutes max) which involve the stakeholders to have conversation about concrete examples of the requirement/stories that are going to be developed.

### Test Driven Development a.k.a TDD

After all the requirements are clear and the team decides to move the task/story card to development, it's time to write ... **tests**. TDD concepts are rather simple:

1. Write tests
2. Run the tests
3. Write/refactor code to make the tests pass
4. Repeat.

This might seems a little backward, but the tests are actually guidlines to help us focus on the desired behaviors that are intended especially if the tests are developed and written after the rules and examples from earlier deliberate discovery step.

In my experience, having tests before writing code reduce testing time and effort, because the data is already set up and I can just target the test I want then let it run without me having to manually navigate and click on the UI.

Nowadays a lot of frameworks support TDD and provide excellent test suits which allow developers to write tests that are easy to read and close to natural language. If you are using Ruby on Rails, `rspec` is an excellent `gem` for writing tests that are very easy to understand.

## All in all

BBD perfectly points out the reasons why I had such a rememberable Friday and has the solutions to offer. Another benefit that I can see which was not mention is that by deliverately discoverying the problem domain allows you to see more clearly what and how long it takes to solve the problem.

Of course it would be meaningless if we don't put any of this into action. If you have been through what I have, then why not try this out for yourself. Use this blog as a guidline or any other source that fits you best and apply BDD to the next few task/stories.

Here is to become a better developer.

Cheers!
