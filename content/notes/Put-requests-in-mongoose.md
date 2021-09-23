---
title: "Put Requests in Mongoose"
date: 2021-09-23T17:51:15+08:00
# weight: 1
# aliases: ["/first"]
tags: ["HTTP"]
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
Within Mongoose, it seems like when I make a put request, I only need to specify the property to update, and the resulting JSON will contain the original properties with only the targeted property changed. In other words, I do not need to specify the whole object to update.

This is convenient. And also the desired behavior. I wonder if this is true across all put requests. 
