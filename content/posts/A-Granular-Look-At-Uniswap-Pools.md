---
title: "A Granular Look at Uniswap Pools"
date: 2021-10-31T11:11:34+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Uniswap", "Dex", "In-Depth"]
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
There's actually a lot of information one can gleam from Uniswaps' [pools page](https://info.uniswap.org/#/pools).

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635650465/Screenshot_2021-10-31_at_11.20.57_AM_mynfu7.png" title="Liquidity pools in Uniswap (31 Oct 2021)" >}}

Out of the five top pools on Uniswap by TVL, 3 are Eth/stablecoin pools, and 2 are Eth/Btc pools.

Clicking on any of the LP pairs, we're redirected to the pools analytics page, where we find the following data:
- total tokens locked
- 24hr volume
- 24hr fees
- historical TVL of pool for up to 5 months
- historical volume of pool for up to 5 months
- a very interesting liquidity chart, which shows us the different levels of liquidity at each pricing band.

The first thing to note is that the Uniswap interface does not show the updated token balance. This below:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1635665942/Screenshot_2021-10-31_at_3.38.51_PM_ehetce.png)

is not accurate. To get the latest token balance, we have to go to Etherscan.

Searching the DAI/ETH pool [contract address](https://etherscan.io/address/0x60594a405d53811d3bc4766596efd80fd545a270#tokentxns), we can see the balance of DAI and ETH in the pool under tokens:
![](https://res.cloudinary.com/dl4murstw/image/upload/v1635666138/Screenshot_2021-10-31_at_3.42.11_PM_iuqva1.png)
