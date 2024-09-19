---
external: false
draft: false
title: “Session Cookie” VS “JWT”, WHAT are they and WHEN to use WHICH
description: Understanding the difference between a session cookie and a JWT is essential for deciding what and when to use which.
date: 2024-09-19
author: Rami Mustaklem
duration: 7 min read
tags:
  - SPA
  - API
  - Server
  - Security
  - XSS
  - Authentication
  - Authorization
  - JWT
  - Cookies
  - Browsers
  - HTTP
ogImagePath: /images/blog/browser.jpg
---

![Photo of browser by [Richy Great]](/images/blog/browser.jpg)
*Photo by [Richy Great](https://unsplash.com/@richygreat) on [Unsplash](https://unsplash.com/photos/white-and-silver-electronic-device-MAYEkmn7G6E)*

## Introduction

There are mainly 2 ways to manage user authentication on the web, in order to track the logged in user and manage their activity on web applications. The first is using a “Server Session”, and the second is a “JWT” (JSON Web Token). These are the 2 main methods to manage a user’s logged in status and also allows for the tracking of the user’s roles and permissions, which is all held inside the data stored within these 2 methods.

I will assume that you already have basic knowledge of the how and why, and presume that the user already filled in a login form on the UI and submitted back to the server. Once the user login is found successful, usually with an email and password, then the following methods’ processes start to take place.

Let’s first talk more about the concept of a “Session”.

## What is a session?

A session, is the interval, phase or duration of time between which, the user has logged in successfully and logged out, whether automatically due to expiration or inactivity, or manually when clicking a button.

It is created or generated, on the server, with the logged in user information, in order to track, whether the user is logged in, whenever the session cookie or token is attached to the requests to be sent back from the browser.

Let’s dive deeper into each method and try to understand the ins and outs for each, in order to be able to compare and evaluate.

## “Server” Session

It’s usually referred to as “Session Cookie”, or “Cookie Authentication”, but cookies can be used to store any data, whether sensitive or not, although sensitive information without encoding, is really a security issue.

After the log in process is successful, the server stores the user information in a “session store” by a unique identifier, and that could be the database, a file or a caching mechanism, like a Redis instance, in order to track this information by the same identifier (session key).

The server sends back many `Set-Cookie` headers, with the name and value for each cookie, for the browser in order to store that session key inside a cookie in the cookie store, along with all of its configurations. Some of these configurations, like the `HttpOnly`, prevent JavaScript code in the browser to access the cookie, at least most modern browsers support this setting. One of the other settings is defining the `Expires` value, for when to let the browser know when this cookie should expire.

In order for the server to track the status and information of the user, these cookies should be sent back to the server with every request “to” the server, for the server to extract the session key, query the user information by that key, and then validate the user’s session.

Attaching the cookies to the requests’ headers is done by setting the value for the `withCredentials` attribute as `true` for the HTTP client used in the browser.

In summary, the traditional “server session” is stored and managed on the server, and the client is restricted from managing or changing any of its data.

## JWT

A JWT, JSON Web Token, is an open standard for securely sharing JSON data between parties. The data within the token is encoded and digitally signed, by a secret key, that is stored on the server, to ensure its authenticity.

According to their website:

> *JSON Web Tokens are an open, industry standard RFC 7519 method for representing claims securely between two parties.*
>

JWT has become widely used in API authentication and authorization workflows, between servers and clients.

A JWT is composed of 3 parts, that are separated by a dot, e.g. `abc.def.xyz`, each part represents an encoded data structure. A header, a payload and a signature.

The header contains information about the token itself, like the algorithm used to sign and encrypt the information.

The second part, the payload, contains the actual data that is being transferred. It could contain the user id, role or any other useful data.

The third part, the signature, a cryptographic signature, a one way hashing signature, that is verified on the server by a secret key to ensure the authenticity of the token.

After the token is generated on the server, it is sent back to the client and stored in the browser, and not the server. All the information related to the user is stored within the token itself. As long as the client holds on to the token, and sends it back to the server with the requests, it is decoded and the user is authenticated, considering it is still valid and hasn’t expired.

This mechanism is called “stateless”, because the server does not need to store or maintain any session “state” for the user.

This is mostly used with “OAuth” or “Single Sign-on”, which is generated by 3rd party services.

## When to use JWT

JWT is stateless, as we mentioned, compact and lightweight, which removes the overhead on the server to store and maintain and verify user authentication state.

This is ideal for when authenticating users between multiple micro-services, the token is decoded on each service and the needed information is extracted and used wherever needed, without the need to reach a specific server or caching service in order to fetch the needed information for the user.

This is also efficient for when providing services through an API, without the need to authenticate a specific user. The API can be reached with a secret key, which is used to generate a token, e.g. for OAuth, and returned to the client asking for the service, and whenever the service is needed the token should be attached to the request.

**Some disadvantages of JWTs include:**

This comes at a security cost, because JWTs are hard to invalidate, when stolen, or the secret key is exposed, until the token itself expires. Once the token is generated, the server does not have any control over it.

Storing the tokens on the client side is also challenging, especially when vulnerable to some security risks like XSS, when not addressed adequately.

JWTs can also become large and heavy when containing a lot of information, and that would slow down network requests.

A good practice to mitigate some security challenges is shortening the lives of the tokens generated. This would reduce the window of opportunity for the token to be misused when compromised.

A part of this is to generate 2 tokens, access token and a refresh token. The access token would expire in a very small amount of time, and the refresh token is paired with the access token with a longer lifespan.

*Looking at the resources section below, attached a link to a guide on how to implement the refresh token approach.*

## Conclusion

It is more secure to use a server session approach, especially when having a monolith application, storing and invalidating on the server. The server has the whole control over the user data and the session data, and can be invalidated by the server itself.

When using micro-services approach, or providing specific API services, it is more manageable to generate JWTs that hold all the needed information within the token itself. Each service within a micro-service architecture, having the secret key, can decode the token and extract the needed data from within. Keeping in mind the security challenges that need to be considered.

---

## Resources

- [YouTube - Session Vs JWT: The Differences You May Not Know!](https://www.youtube.com/watch?v=fyTxwIa-1U0)
- [MDN Web Docs - Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [MDN Web Docs - Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie)
- [Can I Use - HTTP header: Set-Cookie: HttpOnly](https://caniuse.com/?search=httponly)
- [MDN Web Docs - XMLHttpRequest: withCredentials property](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials)
- [JWT Website](https://jwt.io/)
- [Auth0.com - What Are Refresh Tokens and How to Use Them Securely](https://auth0.com/blog/refresh-tokens-what-are-they-and-when-to-use-them/)

---

### Related

[How to secure your Single Page Application (SPA)](/blog/how-to-secure-a-single-page-application)

---
