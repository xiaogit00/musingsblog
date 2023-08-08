---
title: "OpenBB Blows My Mind"
date: 2023-08-08T09:05:31+08:00
# weight: 1
# aliases: ["/first"]
tags: ["financial research"]
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

So I was researching for ways to get a company's historical financials data because I hate clicking through pages of shitty UI and downloading files and all that when all I need is a couple of numbers - when I stumbled upon this open source project called OpenBB. 

To be fair, this wasn't the first thing that came up in my endeavour. Basically, I want to be able to *very quickly* get the historical ratios, balance sheet, and income statement of a company, ideally through a light weight interface like the command line. Initially I thought I was going to use a couple of APIs and then cleaning the data through `jq` or something similar (which is still possible), and that led me to APIs like FinancialModelingPrep and EODHD. EODHD's response design is more like a 'bulk export' approach, where with one call you get every single historical data of a company, whereas FMP's responses are more streamlined and tailored to a specific need. The only thing is, with the free version, I could only get 5 years of historical data, whereas I need the data of way more years, so I was considering upgrading. Before commiting to it, I decided to look around to see if there are any alternatives to this, either in terms of workflow or in terms of data. 

And that was when I discovered OpenBB. After clicking around in their docs and ensuring that they do supply financials, I decided to download it. Installation was pretty seamless (Im using Mac M2), and after that you have a Unix executable file that opens to the terminal. I was surprised that for a shell file there was some kind of backend integration where you needed to sign up, but fuck it, Imma try it of course. 

After that I was pretty much mind-blown. This is a mega feature-rich command line interface that acts, well, as a bloomberg terminal. 

![OpenBB Terminal](https://res.cloudinary.com/dl4murstw/image/upload/v1691457814/Xnapper-2023-08-08-09.22.15_gy5xr0.jpg)

To find the ratio of, say, Google, for the last 20 years, I simply have to do this command: 
`stocks/fa/metrics -t GOOG -l 20`

And this window pops up:
![Google metrics](https://res.cloudinary.com/dl4murstw/image/upload/v1691458584/Xnapper-2023-08-08-09.35.19_ntckjh.png)

20 year financial data, sliced and diced to perfection. There's even an option to export to csv or png!

This is by far the sickest investment research workflow I've discovered. There are ways for you to configure your data sources, so that you can essentially pull data from alternative sources. The only seeming downside is that it takes around 10 seconds to boot up - so whilst it's great if you already have it open, it's still not that fast if you're looking for a quick number here and there.

Incredible find overall. 