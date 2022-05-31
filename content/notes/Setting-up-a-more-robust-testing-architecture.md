---
title: "Setting Up a More Robust Testing Architecture"
date: 2022-02-28T11:19:31+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
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
So, I feel like a very important first step is to identify which large categories of tests that you'll have to do, and whether they belong to backend or frontend, unit tests or integrated tests.

So obviously, one way is to start with the backend, since that is what I've learned.

If we take a look at the backend of the app: we have a couple of routers.

1. StreamsRouter
2. TradesRouter
3. UsersRouter
4. TickersRouter
5. CoinsRouter
6. EmailRouter
7. LoginRouter

It seems like this, altogether, is the functionality of the backend. Most of the routes require their respective database models - every one of them, apart from email and login router.

So obviously, I need a way more systematic way to list down the functionality of each route, and to delineate what are the unit tests that I ought to conduct.

I'll need to look at each component of the backend code, and demarcate their responsibilities and functionalities. Then, I should be able to proceed with writing the tests.
