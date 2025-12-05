---
title: "Recap on Same Origin Policy CORS and CSRF"
date: 2024-01-27T11:40:15+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Web Security", "CSRF", "CORS", "SOP"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

I had a fascinating lecture earlier this week with Prateek Saxena as part of my Web Security course, where we learned about a core pillar of web security, the same origin policy. 

Briefly speaking, the same origin policy disallows different web apps from accessing each other's content and resources through the browser. It's a framework for access control. Just like how User A cannot access User B's files on a Mac OS, website A cannot access website B's HTML, Javascript, CSS, by default. 

At the core of this policy lies the idea of a 'web origin'. As the term suggests, a web origin is the source of a particular request. Strictly speaking, it is defined by a combination of the protocol, host, and port within a URL string: 

```
https://musings.xyz:443

https:// - protocol
musings.xyz - host
:443 - port
```

Within each tab of a browser, there's a label that strictly identifies the 'origin' of the page, and there shall be no direct access between two frames. 

There are some more fine grain rules, however. 

1. A sub domain can access the resources of its parent domain. For instance, app.example.com can access the resources of example.com by default.

2. The code (JS/CSS) running in the same origin can access all objects owned by that origin. This means that a parent origin can access all objects of its children. 

3. It is possible to change the value of the origin of a sub-domain via a browser hack: by using `document.domain` to set the origin to same value. For instance, *app.musings.xyz* and *docs.musings.xyz* can both set their origin to *musings.xyz* and the browser will treat them both as if they belong to musings.xyz. Though this hack, a subdomain can access the resources of its sibling domains. This property, though, is deprecated in the latest browsers. 

4. Paths are excluded from consideration of origins. Meaning, *musings.xyz/posts/123* will be the same origin as *musings.xyz/news/321*. 

5. Since children domain cannot access sibling domains, you can then leverage on the subdomain separation system to isolate requests in your systems. 

However, simply isolating resource sharing will be rather detrimental to the spirit of the internet. Sites *can* and *should* be able to share resources in certain circumstances. How can that be done? Well, via the Cross Origin Resource Sharing policy (CORS). 

So in what cases does the SOP apply, and in what cases does CORS apply?

This differs at the HTTP request level, the DOM API level, and at the browser cookies level.  

#### SOP on HTTP Requests
On the HTTP request level, the browser allows you to *send* GET and POST HTTP requests to any site (but not PUT/DELETE). However, it *disallows* reads and receives of cross-site HTTP requests. In other words, while foo.com might be able to send GET/POST requests to bar.com, when it reaches bar.com, the browser will check the 'Access-Control-Allow-Origin' response header of bar.com's response to see if it contains '*' or the URL of the source HTML. If it does, then the browser will process the response; if not, it'll throw a CORS error. 

When you import the CORS module of Node.js, for instance, what it does in the background is to set the 'Access-Control-Allow-Origin' value of all the responses processed by your server to be whatever you state. The default `app.use(cors())` sets the 'Access-Control-Allow-Origin' header to be '*'. 

#### SOP on DOM API
By default, the Javascript code of site A can only read/write the same-site DOM objects. This property prevents Tab A's Javascript code from manipulating the DOM of Tab B. 

However, cross-origin access is allowed for: 
- certain window.* properties, like `window.close`
- certain location.* properties, like `location.href`
[details](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#window)

The browser allows for the storing of client-side data, in a more 'intuitive fashion' than using cookies - and this is done via the handy localStorage and sessionStorage, i.e. [WebStorage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API). Importantly, for our case, Webstorage only allows same-site access. I.e. the browser will never allow site B to access the localStorage of site A. 

#### SOP on Cookies
Another important way SOP applies is on browser cookies. 

Background: cookies are set by the server on HTTP headers using the 'set-cookie' method. When the server wants to set cookies on the client, it'll send a response with a key-value pair in the `Set-Cookie` header. The `Cookie` header is then used whenever the client sends a HTTP request to the server. It's important to note that the Cookie is a HTTP request header, whilst the *Set-Cookie* is a HTTP response header.

In some ways, it's almost like the server is like stamping all responses from client side with the cookie stamp. 

This [page](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies) explains the process in detail. 

> Fun fact, in Node.js, you can set headers using the following: 
> `response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);`

Now, there are 2 types of cookies. First, the same site cookies: those are set by the origin and sent to it. 

Then there are the third party cookies. These cookies are set by a website other than the one you're currently on. For instance, when you visit google.com and it contains an image from foo.com, then any cookies that are set by foo.com before will be sent to foo.com. In this way, third party resources can learn about which websites you visit. 

Now, Javascript code can access only same site cookies. By default your code at siteA cannot access the cookies of siteB. 

Given that cookies can possibly be used to get information about you, we have to be concerned about security. One potential threat is the man in the middle attack which tries to intercept your cookies. TO prevent that, you can use the `Secure` flag of `Set-Cookie`, like so: 

```
Set-Cookie: id=a3fWa; Expires=Thu, 21 Oct 2021 07:28:00 GMT; Secure; HttpOnly
```

A cookie with the `Secure` attribute is only sent to a server with an encrypted request over the HTTPS protocol. In other words, only sites with HTTPS can set secure cookies, and cookies with Secure attributes will only be sent to HTTPS sites.

It is never sent with unsecured HTTP (except on localhost), and insecure sites (http) also can't set cookies with the `Secure` attribute.

To prevent site javascript from tampering with the cookies, one can also use the `HttpOnly` tag. This tag will prevent the Javascipt Document.cookie API to access the cookie, such that its only function is to be sent to the server. This helps mitigate cross-site scripting attacks, where attackers try to gain control of your cookie using malicious Javascript code. 

It's important to note that whilst HTTP sites cannot read HTTPS secure cookies, it can actually set (or overwrite) HTTP secure cookies. For instance, *https.example.com* sets a cookie on the client `Set-cookie: SID=123; secure`; when we visit *http.example.com*, it *can* rewrite the cookie on client to `Set-cookie: SID=456; secure`, the browser will overwrite the previously set 'secure' cookie by our HTTPS server. This, notes Prateek, is a bug in web standards. 

In general though, if you're building websites and you're not setting the HTTPonly flags and the secure flags, you're still opening yourself up to serious attacks.

### CSRF / XSRF

In the final section of the class, we moved on to CSRF / XSRF, which stands for cross site request forgery. 

The key idea here is this. When evil.com sends a post request to bank.com, it is bank.com's cookies that will be sent to it. 

Any site can essentially trigger a request with the target site's cookies attached to it. A common attack pattern will be, suppose you're logged into 'bank.com'. It is possible for *evil.com* to send a post request to *bank.com* and when *bank.com* receives the request, it also receives the current cookies that were set during your login. 

One older way of preventing this is for the cookie to have a **SameSite** flag. For instance, bank.com should set cookies with SameSite=Lax. 

1. `Set-Cookie: SID=1; SameSite=Lax`
If set to lax, the cross-site HTTP request from evil.com will not contain the cookie. 

2. `Set-Cookie: SID=1; SameSite=None`
If set to None, the cross-site HTTP Post still contains the cookie. 

However, in practice, this is not the default method for preventing CSRF. Browsers now, by default, block cross-origin sharing. Web servers need to explicitly state which resource sharing it allows. 

For instance, by default, when evil.com tries to send a POST request to bank.com, it'll check with bank.com to see if its response headers contain the URL of evil.com. If it does, then it'll allow the request. This is also illustrated earlier. 

Finally, if this level of security is not enough for you, you can also use defensive programming methods, such as using a secret validation token. One way to implement is as follows. When a logged in user loads a page with the form, your server generates a secret value that is unique to that particular user. Whenever the server receives a POST request for the form, it can check whether the secret value is a valid one. The reason why CSRF will fail is because the SOP prevents evil.com from accessing the DOM of bank.com to retrieve that value. 

That's about it for this lesson, a lot of things to digest. 