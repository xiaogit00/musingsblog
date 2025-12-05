---
title: "Reached Some Impasse"
date: 2025-10-20T08:24:05+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
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
I am following along to the coursera implementation of the transformer. For what it's worth, it's quite am ambitious exercise, taking students through the exact implementation step by step, as well as providing unit tests for each function along the way. 

However, I am at the part where I have implemented the encoder layer, and I feel like I am losing track - of all the different things happening, of all the weights and variables produced, and of the series of transformations on the data. 

I can't help but to feel that this might not be the best way to get all these into your head - and that a better solution could be found (at least for the learning phase) by attempting to visualize everything in Clojure. 

Learning it in the Pythonic approach feels so futile. There's so many hidden states and weights to take care of. I feel like I need a better approach, but the instructions are wanting. 

Would it be possible, at this point, to give this approach a rest, and instead, try to implement the same in Clojure, functionally? 

I feel like it's worth giving it a shot. 

Instead of going *downwards* from linear regression all the way through to Transformers, I am going to try to start from transformers, and begin visualizing each of the components within this stack. 

***

Okay, after googling what is going on in the Clojure ecosystem for this, it might not be the best bet. There might be too many things to handle for what I am going to implement. 

I might be better off trying to implement this functionally in Python, than to try to implement this directly with Clojure. 