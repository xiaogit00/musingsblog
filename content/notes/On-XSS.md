---
title: "On XSS"
date: 2024-02-10T15:45:10+08:00
# weight: 1
# aliases: ["/first"]
tags: ["XSS"]
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
In the web security class this week, I learned another class of attacks: cross site scripting attacks, or XSS for short. 

## What is XSS?
In essence, an XSS attack occurs when site A manages to get a script that it wants to run into site B. An example of a *reflected* XSS is as follows: 

Evil.com gets Joe to click on a malicious link. That link then directs Joe to a bank.com domain, along a query parameter (e.g. `bank.com?q=BALANCE`). Evil.com knows that bank.com's code is badly written, such that **anything sent along the query parameter will be *reflected* in the html file, unsanitized**. As such, it injects `<script>sendcookies</script>` into the query. When the HTML page loads, the script is ran, and Joe's cookies are then sent to Evil.com. 

In this case, evil.com manages to run an arbitrary script on the html code served by bank.com. 

In this class of vulnerability, the main source of error is bank.com's html code running the query values injected via the URL as commands. 

In another variant of this attack, a user uploads a script tag into a plain text user input area - the so-called Samy worm. Whenever visitors land on Samy's homepage, the script is ran, and user's cookies can be sent. This is also known as a **persistent XSS**. Worth noting is how scripts can also be ran via img tags, such as the following:

`<img src="javascipt:alert('xss')/>`

Whilst googling around, I've also managed to see a real life example of this: [XSS on TikTok](https://hackerone.com/reports/968082). It's certainly interesting to see an example of XSS in action - and to see the process of a user submitting a bug, the team responding and triaging the issues over the next few days, eventually raising its severity from medium to high. 

As a web dev, then, it's important then to keep a few things in mind: **don't reflect query values in your HTML code**. That'll leave your web app vulnerable to XSS attacks. You should be careful of this for all pages. 

