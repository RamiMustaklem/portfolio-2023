---
external: false
draft: false
title: How to Secure your Single Page Application
description: Understanding the security risks is crucial to understanding how to secure an SPA and mitigate threats.
date: 2024-09-01
author: Rami Mustaklem
duration: 7 min read
tags:
  - SPA
  - API
  - Server
  - Security
  - XSS
  - CSRF
  - Authentication
  - Authorization
  - JWT
  - Cookies
  - Browsers
  - HTTP
  - CORS
ogImagePath: https://unsplash.com/photos/matrix-movie-still-iar-afB0QQw
---

![Photo of matrix by [Markus Spiske]](/images/blog/spa.jpg)
*Photo by [Markus Spiske](https://unsplash.com/@markusspiske) on [Unsplash](https://unsplash.com/photos/matrix-movie-still-iar-afB0QQw)*

## Introduction

On the internet, there are mainly 2 types of web applications, websites that are rendered using a server returning mostly HTML content back to the browser, which is largely imperative for SEO and SEM. The other kind is web content management systems and SaaS (Software as a Service) applications that are mostly used for back office or content management that do not need to be particularly rendered on the server or crawled and indexed by search engines, thus the load is passed on to the user’s browser, loading JavaScript libraries and frameworks and letting the client side (browser) handle most of the load for rendering and repainting designated parts of the UI, leading to a smoother, performant and more responsive and fluid user experience.

These kinds of applications, rendered and managed in the browser, are called “Single Page Applications” (SPAs), because of the reason that the server serving this kind of app is only serving one page, the index page, and within that page passing all the needed client side code to the browser in order to handle the rest of the application’s utilization and user interaction with the provided interface.

This approach changes the extent of the app and opens up another level of communication for providing and managing data back and forth between the SPA and a Server (usually API) that needs managing and securing.

In order to understand how to secure SPAs, first we need to also understand the underlying basics and concepts of securing web applications, such as authentication, authorization, secure HTTP, CSRF tokens and Session management.

So let’s dive into some critical web security concepts before we start addressing their exploits and their respective mitigations.

## Authentication vs Authorization

On the web, authentication and authorization is usually referred to as “Auth”, but there is a difference between them. Authenticating a user is the process of granting the user the right to enter your application as whole, through registering to the app with private credentials (usually email and password) and filling a login form with these credentials, which are sent to and validated on the server.

On the other hand, authorization is the process of assigning and managing each user’s designated roles and permissions on the different parts of the application. For example, an “Administrator” role, with permissions to “update” or “delete” certain resources, should be able to do so, yet a “User” role, with permission only to “view” certain resources, should be able to view them without having the ability to “update” or “delete” any of them.

Having covered the “Auth” part, we can move on to the part after, which is managing the session of the logged in user.

## Session Management

As soon as the user’s credentials are found valid on the server, the server creates a session, in order for it to manage the authenticity of the requests from the client side application. Some parts or resources of the application are normally not public facing and should only be accessed by the users of the application.

So there are 2 kinds of session management to implement, the first uses a “Session Cookie” or a “Session Key”, and the other uses a “JWT Token”, which you’ve probably heard of, and is implemented mostly in most tutorials on the internet.

The difference between Session Cookies and JWT Tokens, is the location the user data is stored in. A Session key is stored and managed on the server, accessing user data and validating the validity of the session when retrieving it with the session key itself. Basically a generated unique identifier to store and retrieve the data on file or database or a caching server like “Redis”. JWT is generated on the server and stored and managed on the client, usually the browser’s local storage, although it is not recommended, it could also be stored in a browser cookie, with the token holding all the details needed for the user, encoded and decoded on the server, including the expiration date of the token.

Whether it’s a session key or a JWT token stored in the browser’s cookies, it is sent with every request from the client in order for the server to acknowledge the user’s state, authenticated or not.

Now that we’ve covered the important basics, let’s go deeper into what could happen to a Single Page Application, security-wise.

## SPA’s Security Threats

Understanding potential threats is key to being able to protecting against them, so it’s crucial for one to know and understand them in order to be able to mitigate and put in place the necessary measures to prevent such threats, especially when such applications are mostly loaded on the browser, as a result, facilitating for malicious users to misuse the application or hackers the ability to hijack the user sessions or data.

### Cross-Site Request Forgery (CSRF)

#### Problem

An authenticated app user, is tricked into clicking a link, on another site or app, that performs a particular action, like changing the user account email or password. Underlying the malicious link could be a form submitting such action to a known endpoint of the app, causing the unauthorized action to be submitted to the API that is on the other side of the SPA.

#### Solution

If you’re using a cookie based authentication, whether with a session key or a JWT, then assign the cookie `SameSite` attribute with `Lax` value to prevent the cookie being sent to the server from another domain address. Unfortunately, although this approach is supported by most web browsers, it is not supported by 100% of browsers, so it’s important to cover the rest with another fallback approach.

Another way to achieve this is through CSRF tokens, “random” strings, that are generated on the server and sent back to the client side in order to be sent back with each request that is submitted by a form, that is usually a `POST`, or could be some other destructive action for updating or deleting user data.

Other solutions, like verifying origin and referrer headers, setting custom headers, are also possible to implement.

### Cross-Site Scripting (XSS)

#### Problem

An attacker injects malicious scripts into a form input, and saved to the database. When this script is loaded on the client side and gets rendered on a page without proper sanitization, it is then executed in the context of the user's browser, potentially stealing session cookies or tokens or any data that is stored on the browser, or even redirecting the user to a malicious site.

#### Solution

Always assume malicious activity from the client side, so if you have any form input submission always **Validate and Sanitize** all user input.

Avoid storing JWT tokens or any other sensitive data in `localStorage` or `sessionStorage`.

Other security measures also address **XSS** and **CSRF** problems, that we’ll cover in a later section.

### Sensitive Data Exposure

#### Problem

Assume nothing is secure on the client side, because all the information sent to, or stored on the browser, is visible to the users and is also accessible by scripts, thus giving malicious scripts the opportunity to modify or access sensitive data.

#### Solution

Avoid storing any API-keys or secrets, whether for your own server or any 3rd party service, or any sensitive information on the browser. The API should be responsible to reach any 3rd Party services. Sensitive information is stored on your server, and the server is responsible to relay any communication with 3rd party services for the client side.

We’ve covered the main problems and their respective solutions until now, let’s point out the general coding practices and implementations to avoid such exploits.

### Best Practices

Sealing a single page application holds a few layers of security measures, but it’s important to follow some critical practices in order to tighten the knots and bridge the gaps.

1. Use **HTTPS** and “**HTTP-Only**” Cookies to prevent JavaScript from accessing cookies on the browser.
2. Define a strict **Content Security Policy (CSP)**, for loading scripts and images only from known and trusted sources.
3. **Secure Communication**, use only “HTTPS” with an “SSL certificate” to encrypt data transmitted between the client and server. Unsecured communications can lead to attackers intercepting and manipulating data through man-in-the-middle attacks.
4. Setting strict “**CORS**” configurations on the server and making sure requests received on the API, are from a set of predefined trusted origins.
5. Correctly implement user authorization, to prevent users from accessing resources they shouldn’t.
6. Also sanitizing input, received from the client, on the server.
7. Keep **dependencies and libraries up-to-date** to mitigate known vulnerabilities.
8. **Handle Errors Properly** using generic error messages when logging errors to the client and avoid exposing stack traces or sensitive information.
9. Use a service like **Cloudflare** to hide your IP address behind a proxy to minimize exposure to malicious activity.

### SPA/API - Same Origin

Last but not least, my most recommended approach, is hosting the web app and its API behind it on the same server instance or web address whenever possible, for example, a load balancer or a private network. This way the communication between the instances is secured within a private network.

Using a framework like Angular, or a meta-framework like NextJS, gives you the ability to close on your client application with your own backend specifically serving the client application. Angular provides a way to proxy your API calls with proxy configurations, and something like NextJS provides a way to communicate with the app own backend through something called “server actions” or even API routes built-in the NextJS framework, giving the ability for the client app to call the framework’s own server, having any sensitive information or API keys or secrets on it, and there relaying any requests to the database or 3rd party services.

You can even build your own simple ExpressJS service to wrap your client app within and serve its index page and passing on communications and data. This way both the client and the API is on the same origin, keeping any sensitive keys secured on the server.

## Conclusion

Mitigating these threats involves a combination of secure coding practices, proper configuration, and regular security audits, in order to protect against potential data breaches or unauthorized actions.

Securing a Single Page Application (SPA) also involves understanding common security risks and implementing coding best practices, while also involving securing any API supporting it.

Keeping up and enhancing security takes time and effort, but by staying vigilant and proactive, you can protect your SPA and ensure a safer user experience.

---

### Resources

- https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
- https://developer.mozilla.org/en-US/docs/Glossary/HTTPS
- https://web.dev/articles/samesite-cookies-explained
- https://web.dev/articles/strict-csp
- https://developer.mozilla.org/en-US/docs/Web/Security/Practical_implementation_guides/CSRF_prevention
- https://www.youtube.com/watch?v=fyTxwIa-1U0

---
