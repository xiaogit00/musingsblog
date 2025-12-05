---
title: "Setting Up Google Analytics"
date: 2021-08-20T10:13:26+08:00
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
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
I am fascinated by how simple it is to set up Google Analytics for my site. All I did, was a few simple steps to make it working on my Hugo blog.

1. Create a Google Analytics Account and Property (website)
2. Get the measurement ID `G-2FGC0xxxx`, found under Admin-->Data Streams of Analytics Dashboard and paste it into config.yml, like so:
```markdown
googleAnalytics: G-2FGC0CNJX2
```


And *that's it!* That's all it takes for Google Analytics to work and begin capturing data on your site. Usually, an additional step is needed for you to paste the JS code into the sections of the sites you want to capture data for (usually the headers), but for this particular theme, you [don't have to](https://github.com/adityatelange/hugo-PaperMod/issues/16).

This is because the Google Analytics template:
```markdown
 {{ template _internal/google_analytics.html}}
```
is already included in the head.html of layouts, and is automatically set to be enabled once the ENV is set to production.

Brilliantly designed. In the next post, I'll be fan girling over Google Analytics 4's Real time data feature.
