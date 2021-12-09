---
title: "Querying Blockchain Data With Covalent"
date: 2021-12-08T15:06:33+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Covalent", "API", "On-chain", "Data"]
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
Covalent is an API service that provides us with custom endpoints to query and gather blockchain data. In this article, I'll provide a guide to navigating and using Covalent API data.


### General API call format

First, the general format of an API request to Covalent is as follows:

## `https://api.covalenthq.com/v1/<endpoint>/?key=API_KEY`

`<endpoint>` refers to the API endpoint that Covalent provides for us.

`API_KEY` is the free API key that you can get [here](https://www.covalenthq.com/). It has the following format: `ckey_53d9xxxxxxxxxxxxxxx`

A complete request for live price data of BTC, for instance, looks like this:

`https://api.covalenthq.com/v1/pricing/tickers/?tickers=BTC&key=ckey_53d9xxxxxxxxxxxxxxx`

Get an API key, go to your browser to try it! You can replace `BTC` with any ticker you want from this list:


# Getting pricing data with Covalent API

### CURL
If you want a quick and dirty overview of the spot price of a token, you can do so with the following command

The simplest way is to use curl from the command line.

```
curl "https://api.covalenthq.com/v1/pricing/tickers/?quote-currency=USD&format=JSON&key=ckey_53d9f55e830446a3b8cedcd9ab9"
```
