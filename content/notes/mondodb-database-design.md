---
title: "Mondodb Database Design"
date: 2021-08-23T11:18:58+08:00
# weight: 1
# aliases: ["/first"]
tags: ["mongodb", "database design"]
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

So whilst I was trying to design the database for my investment tracking app, I stumbled upon the question of what are the differences between traditional database designs and NOSQL, and what's the most optimal way to design the NoSQL database for best performance.

After a quick look around, I [discovered](https://www.mongodb.com/developer/article/mongodb-schema-design-best-practices/) that there are *no special things you need to do to design NoSQL schemas*, apart from simply treating it like you would store info in a python dictionary: through the JSON structure.

An important point is, it's almost always better to store data 'pythonically' than to store it relationally (which you can, via $lookup operator, which is similar to a JOIN). There are performance overheads for the $lookup operator.
