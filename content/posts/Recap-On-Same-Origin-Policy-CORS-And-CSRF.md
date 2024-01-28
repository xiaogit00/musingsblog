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
draft: true
hidemeta: false
comments: false
description: ""
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
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

Briefly speaking, the same origin policy disallows different web apps from accessing one another's content and resources through the browser. It's a framework for access control. Just like how User A cannot access User B's files on a Mac OS, website A cannot access website B's HTML, Javascript, CSS, by default. 

The strict definition of a web origin refers to a combination of the protocol, host, and port within a URL string: 

```
https://musings.xyz:443

https:// - protocol
musings.xyz - host
:443 - port
```

Within each tab of a browser, there's a label that strictly identifies the 'origin' of the page, and there shall be no direct access between two frames. 

There are some more fine grain rules, however. 

1. A sub domains can access the resources of parent domains. If this site has app.musingsondefi.xyz sub-domain, it can access the resources of musingsondef.xyz. 

2. The code (JS/CSS) running in the same origin can access all objects owned by that origin, which is an obvious point. That means that a parent origin can access all objects of its children. 

3. It is possible to change the value of the origin of a sub-domain via a browser hack: using `document.domain` to set the origin to same value. For instance, *app.musings.xyz* and *docs.musings.xyz* can both set their origin to *musings.xyz* and the browser will treat them both as if they belong to foo.com. (However, I am still unclear as to the motivations of doing so.) - this property, though, is deprecated in the latest browsers. 

4. Paths are excluded from consideration of origins. Meaning, *musings.xyz/posts/123* will be the same origin as *musings.xyz/news/321*. 

5. Since children domain cannot access sibling domains, you can then leverage on the subdomain separation system to isolate requests in your systems. 

All these are really interesting. 

However, simply isolating resource sharing will be very detrimental to the spirit of the internet, which is largely a composable unit. Sites *can* share resources - this is done via the Cross Origin policy.

So in what cases does SOP apply, and in what cases does CORs apply?

It turns out, that different levels of application occurs for different classes of things. 

#### SOP on HTTP Requests
On the HTTP request level, the browser allows you to *send* GET and POST HTTP requests to any site (but not PUT/DELETE). However, it *disallows* reads and receives of cross-site HTTP requests. 

For instance, foo.com might be able to send GET/POST requests to bar.com, but the browser will disallow reads/receives of the requests from foo.com. 

It'll always allow the reads/receives of the same-site HTTP requests. 

#### SOP on DOM API
By default, the Javascript code of site A can only read/write the same-site DOM objects. This property prevents Tab A's Javascript code from manipulating the DOM of Tab B. 

However, cross-origin access is allowed for: 
- certain window.* properties, like `window.close`
- certain location.* properties, like `location.href`
[details](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#window)

The browser allows for the storing of client-side data, in a more 'intuitive fashion' than using cookies - and this is done via the handy localStorage and sessionStorage, i.e. [WebStorage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API). Importantly, for our case, Webstorage only allows same-site access. I.e. the browser will never allow site B to access the localStorage of site A. 

