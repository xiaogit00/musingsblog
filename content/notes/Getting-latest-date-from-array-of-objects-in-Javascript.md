---
title: "Getting Latest Date From Array of Objects in Javascript"
date: 2021-08-25T18:07:33+08:00
# weight: 1
# aliases: ["/first"]
tags: ["javascript", "learnings"]
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
Let's say I have an array of objects, like so:

```javascript
let trades = [
  {
    id: 1,
    date: "2019-04-23T18:25:43.511Z",
    asset: "BTC",
    orderType: "Limit Buy",
    platform: "Coinhako",
    price: 46580,
    price_denom: "USD",
    nominal_price: 32313 ,
    amt: 0.003,
    value: 139.74,
    value_denom: "USD",
    fees: 4,
    fees_denom: "USD",
    comments: "good buy"
  },
  {
    id: 2,
    date: "2019-05-23T18:25:43.511Z",
    asset: "BTC",
    orderType: "Limit Buy",
    platform: "Gemini",
    price: 38000,
    price_denom: "USD",
    nominal_price: 32313,
    amt: 0.02,
    value: 760,
    value_denom: "USD",
    fees: 14,
    fees_denom: "USD",
    comments: "not bad buy"
  },
  {
    id: 3,
    date: "2019-05-23T18:25:43.511Z",
    asset: "Alibaba",
    orderType: "Limit Buy",
    platform: "SC",
    price: 196,
    price_denom: "HKD",
    nominal_price: 34.07,
    amt: 100,
    value: 19600,
    value_denom: "HKD",
    fees: 128,
    fees_denom: "HKD",
    comments: "bad buy"
  }
]
```

I want to get the latest date.

The following [solution](https://stackoverflow.com/questions/36577205/what-is-the-elegant-way-to-get-the-latest-date-from-array-of-objects-in-client-s) will do the trick:

```javascript
const latestDate = new Date(Math.max(...trades.map(trade => new Date(trade.date))))
```

`...trades.map` is the property spread notation. It 'spreads' out an array into a list of arguments. Basically removes the square brackets.

In my case, `trades.map(trade => new Date(trade.date))` will return an array of dates. Literally this:
`[Date Wed Apr 24 2019 02:25:43 GMT+0800 (Singapore Standard Time), 1: Date Fri May 24 2019 02:25:43 GMT+0800 (Singapore Standard Time)]`

The `...` before, will convert this array into simply 2 arguments, which you pass into Math.max() which will return you the latest. Then you of course create a new date out of it.

Brilliant. 
