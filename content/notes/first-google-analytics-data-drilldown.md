---
title: "First Google Analytics Data Drilldown"
date: 2021-08-22T16:08:05+08:00
# weight: 1
# aliases: ["/first"]
tags: ["google analytics"]
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
searchHidden: true
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
After having set up Google Analystics on Friday, I am about to have some fun analyzing the data.

Navigating through Google Analytics isn't the most intuitive. Well - it's easy enough, but it still takes some time for me to figure how what does what on the website. The reports page really doesn't give me much insights about user behavior on my site. To get real insights, I had to go to explore page. There, I could set up a new exploration (much like cards in Metabase), where I could get more granular detail on my data.

Playing around with the different variables and tab settings, I found out that this is what I have to do most of the time:

Segments is about filtering Users by certain segments, such as age, gender, country, page referrer, platform, device, etc.

Referring to Google support, a dimension is an attribute to your data. To put it into SQL terms, it is a Groupby.

You can also add a second Groupby, or Dimension. This will break down your data into smaller groups.

A metric is simply your Select metric1, metric2 FROM xxx in SQL terms.

Segment is simply a filter for User data.

It's like WHERE User.location === xxx.

So a simple way to resolve what you need to know is to ask your question in advance, and try to drill down to get the data, just like in SQL.
