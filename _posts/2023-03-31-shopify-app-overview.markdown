---
layout: post
title: "Shopify App Overview"
date: 2023-03-31
categories:
  - ruby
description: A high level view and some key concepts when starting to work on a Shopify app
---

A full documentation link can be found here: `https://shopify.dev/docs/apps/getting-started`

### What’s the purpose of a Shopify app?
* Extends functionality of Shopify
* Add more features or improve some existing features (e.g. Custom checkout flow)
* Integrate with external services (e.g. delivery services like UPS)
* Pull and use Shopify data in your app, platform, integration

![purpose_shopify_app](https://cdn.discordapp.com/attachments/1090947545477427264/1091310147013455872/image.png)

### How apps fit into Shopify?
* Use Shopify APIs to read and write user’s store data in response to different events (webhook events)
* Extending new features inside existing parts of the Shopify admin
* Enhance the way stores display info to customers

### How to start building an app?
* Before starting make sure you have a shopify partner account
* Use ShopifyCLI to take advantage of the app template
* Or can always start from scratch (using APIs and follow authentication flow, then call their APIs to read and write data)

### Authorization and authentication
* Must complete authorization and authentication before working with merchant and Shopify data
* Authorization means the store merchant gives the app permission to access data in the store e.g. orders, products, etc.
* authentication means Shopify verify the identity of the user of the app; all apps connecting to APIs must authenticate before making a request
* authentication simplified by using a access and refresh token

### Types of authorization and authentication methods
* Depending on how you create your app and the components your app uses, you will be prompted to use different authorization and authentication methods
* Apps created with Shopify CLI or through the partner Dashboard use OAuth
* Apps embedded in Shopify admin using App Bridge use sessions tokens
* Apps created in Shopify admin use access tokens generated in the Shopify Admin

![types_of_apps_and_auth_methods](https://cdn.discordapp.com/attachments/1090947545477427264/1091311003200921600/image.png)

### Summary:
This note captures at a high level what the things you need to follow and use when starting to work on a Shopify app. In my experience, when creating a Shopify app, many people use Shopify CLI with Shopify supported libraries (Ruby, Python, Js, etc.).

Last but not least, having a solid understanding about OAuth is quite beneficial especially if you are not using Shopify app templates.
