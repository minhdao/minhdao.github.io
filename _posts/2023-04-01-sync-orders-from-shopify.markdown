---
layout: post
title: "Shopify orders sync"
date: 2023-04-01
categories:
  - ruby
description: How would you sync orders from Shopify?
---

Some time ago I was tasked to integrate Shopify into the company's existing system. This note is about some design decision and some potential issues when syncing orders Shopify that you might want to think about sooner rather than later.

## The task
* Sync orders from Shopify
* Receive and process order data from webhook events: `orders/create`, `orders/edited`, and `refunds/create`
* Process the order data to: 
* Create new native app order
* Update existing order

## The design idea
The goal of the design is to keep the Shopify functionalities isolated without introducing the Shopify dependency to the rest of the existing application. Creating a new Rails engine to encapsulate the Shopify functionality with a dedicated set of database tables seems to be a good option.

The Shopify engine still needs to take the Shopify event data and turn it into the actual orders in our application. To do this we can create some services inside the engine to convert the data into the application orders. While this approach gives great isolation to the engines, it introduces some maintainability issues down the road, because we now have 2 separate places that do the same thing. Furthermore, we think that it would be better to separate operations that create or update the data inside application order tables from the Shopify engine.

Therefore, we decided to create an `OrderService` that can be called upon, by main application or any other engines, to perform operations related to the application orders.

The engine is the place to receive, save the Shopify data and build the application order object then send it to a separate service to actually create the application order. The design can be summarized into a sequence diagram as below:

![shopify_order_sync](https://cdn.discordapp.com/attachments/1090947545477427264/1091648268682928190/image.png)

We can go a bit deeper into the details of important parts of the design such as the database tables and the webhook endpoints.

### Database tables
We want to treat each request calling to our order webhook endpoint independently even though many requests can be related to the same orders. Data from Shopify should be persisted in case we need to re-create or fix the orders or debug. Therefore, each row in the requests table represents a request from Shopify.

On top of this, we also want to record any errors happening when processing each request on our side, because many things can go wrong when processing complicated data such as a Shopify order data. The least we can do is to keep track of the errors that happen to an order request. An errors table is created for this purpose.

So far the database tables to support Shopify order syncing looks like this:

![shopify_order_sync_database](https://cdn.discordapp.com/attachments/1090947545477427264/1091648327214444644/image.png)

### Webhook endpoint and application logics
This is the destination for all webhook order requests from Shopify. In Rails, this could simply be a controller defined inside the Shopify engine, and decides the logic flow for different type of webhook event data e.g. `orders/create`,  `orders/edited`,  `refunds/create`.

As soon as the event data is persisted inside the application database, the endpoint sends a response back to Shopify to let them know the data has been received. 

Depending on the type of event, the corresponding worker will be called to handle the data. The worker can then call other related services to accomplish some goal. This part varies greatly depending on the app. However, I think the *** most important thing here is to be aware, capture, and handle (if possible) different types of errors when processing the Shopify order event data ***.

## Things to pay attention to when implementing order syncing from Shopify, just to name a few
* Make sure the request to your application server is from Shopify
* Different type of events might contain completely different data structure
* Events received from Shopify are not guaranteed to be in the correct sequence .E.g. you might receive update event before create events
* Multiple update events might arrive in incorrect sequence. E.g. The first update to the order was made at `9:00:00`, and the second update was made shortly after `9:00:03`, but when the events arrive at your server the later one arrives first.
* Same event might be sent multiple times if your application does not respond correctly and timely

Depending on what the application wants to do with the event data, there can be even more cases that need to be handled.

## Summary
Shopify is an incredible e-commerce platform with great support for integrations. However, there are complex use cases that can only be handled inside the realm of your application. Even if you have already published your app to the Shopify store, there will surely be more improvements and bugs that need to be resolved. 

Hopefully, the information in this note can help you get started and give some heads up about the potential issue that your application might encounter somewhere down the road.
