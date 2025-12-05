---
title: "Polygon Fund"
date: 2021-12-24T10:43:40+08:00
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
So, I'll begin with some low hanging fruits.

Let's say, I've got a fund of 2K USD in Polygon.

How should I manage it for it to grow to 4k?

Let's check out what I already know.

### Aave

What does Aave allow me to do? Well, first of all, I could deposit my DAI to earn interest and Matic rewards, whilst allowing me to borrow WETH or WBTC, to earn more rewards.

It's an attractive position to be in whilst I figure out what next.

So let's just try that.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640314617/Screenshot_2021-12-24_at_10.56.44_AM_p1z1w6.png)

First of all, we see that the APY for DAI is pretty attractive-ish, going at around 4%. For a stablecoin with very comparatively little risk, this is not too bad.

>I need to also refresh my memory on the difference between APR and APY. Let's say Aave pays rewards for every block, which is 2 seconds. A 4.23% APY means that, if I take $861 invested, and the returns are continuously reinvested at every block, that means that at the end of the year, I will get $861 * 1.042 = $897, which is around a $36 profit. Not too bad for a relatively risk free investment.

> The right way to do this is. Let r be the rate every period, which is 2 seconds. Within a year, there are

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640314486/Screenshot_2021-12-24_at_10.54.07_AM_rn7o15.png)

Looking at the
