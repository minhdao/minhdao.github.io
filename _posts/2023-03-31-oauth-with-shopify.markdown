---
layout: post
title: "OAuth with Shopify"
date: 2023-03-31
categories:
  - ruby
description: What is OAuth? How does it work in Shopify?
---

The full documentation can be found here `https://shopify.dev/docs/apps/auth/oauth`

## What is OAuth?
* Authorization flow/protocol/framework that enables a third party to obtain limited access/permission to an HTTP service
* Authorization is a process of giving permission or authority
* Authentication is a process of verifying the identity of a user

## What are the problems OAuth trying to address?
* User credential info:
  - User need to share credentials with the app, so the app can pull and use data
  - App need to store the credentials
  - This might cause the credentials to be stolen
* If a merchant has many apps, then they need to share the credentials with all apps
  - If the use want to revoke the access of one app, then all other apps will need to update to the new credentials
* If an app have a merchant credential then, it can access other data that it might not need or should not look at

## OAuth flow in a Shopify app
1. App sends authorization request to the merchant and redirect merchant to a Shopify permission grant page
2. Merchant can see the set of permission required by the app and choose to grant permission
3. A temporary authorization grant code is sent back to the app
4. The app use temporary grant code and ask shopify for access token
5. Shopify validates the grant code to make sure the merchant allow a set of permission
6. Shopify sends access token back to the app to use

![shopify_oauth_flow](https://cdn.discordapp.com/attachments/1090947545477427264/1091313317039067166/image.png)

## Some security measures for Shopify OAuth flow

The full documentation can be found here `https://shopify.dev/docs/apps/auth/oauth/getting-started#step-2-verify-the-installation-request`

If you plan to implement OAuth flow by yourself, then you need to follow Shopify OAuth guide and pay close attention to some security aspects listed below.

### Verify the installation request
When a merchant click on to install your app, your app will receive a `GET` request to the ***App URL*** (you specified when setting up your app in the partner account) wit the following params

```
"code=0907a61c0c8d55e99db179b68161bc00&hmac=700e2dadb827fcc8609e9d5ce208b2e9cdaab9df07390d2cbca10d7c328fc4bf&shop={shop}.myshopify.com&state=0.6784241404160823&timestamp=1337178173"
```

Every request sent from Shopify will include the `hmac` param. You need to check the authenticity of the request by validating the `hmac` value.

To verify the authenticity of the request you need to:
1. Remove the `hmac` param from the original request
2. Process the the request URL (without the `hmac`) through HMAC-SHA256 hashing function with the `client secret` of your app
3. Compare the original `hmac` value with the hashed value from step 2, and make sure they are the same

If the `hmac` are mismatched then the request is properly tempered and cannot be trusted.

### Redirect merchant to the grant screen
Your app must redirect the merchant to a grant screen where they will be presented with the permission scopes your app requires and then they can choose to allow or deny the permission. The app redirect the merchant to the grant screen by constructing a URL looking as follow:
```
https://{shop}.myshopify.com/admin/oauth/authorize?client_id={client_id}&scope={scopes}&redirect_uri={redirect_uri}&state={nonce}&grant_options[]={access_mode}
```

A param to pay close attention to here is `nonce`. This params should be something that is:
1. Randomly generated
2. Unique for each authorize request
3. Set as a signed cookie to the browser with the `nonce` value
4. Used to check authenticity of the later request to your `redirect_url`


### Confirm installation
After a merchant clicks on the install app button, they will be redirected to the `redirect_url` param. Shopify also attaches some more params to your redirected URL:
```
https://example.org/some/redirect/uri?code={authorization_code}&hmac=da9d83c171400a41f8db91a950508985&host={base64_encoded_hostname}&shop={shop_origin}&state={nonce}&timestamp=1409617544
```
A param to pay attention to is the `state` param. This param should have the same value as the `nonce` param value you set earlier. If you set the `nonce` value as a signed cookie, then you can check the cookie on the merchant browser to see if they are the same.

Another parameter to pay attention to is the `hmac`. You should verify this value like how you did above.

Last but not least, the shop param must be a valid shopify shop hostname ending with `myshopify.com`.

If any of the security measures above failed then the request should be rejected with an error, and must not continue.


### Checking `nonce` values
The image below illustrate how the `nonce` value is used to validate requests coming to an app server.

![nonce_validation](https://cdn.discordapp.com/attachments/1090947545477427264/1091314658280996894/image.png)

## Summary
OAuth is a great framework to perform authentication. It helps to address problems with keeping user credentials and limiting access scope to only the data an app might need.
However, when working with or implementing the OAuth flow, some more security is put into verifying the request authenticity as mentioned above.
