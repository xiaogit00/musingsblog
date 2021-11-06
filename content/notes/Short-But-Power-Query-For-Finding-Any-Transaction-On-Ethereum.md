---
title: "Short but Power Query for Finding Any Transaction on Ethereum"
date: 2021-11-06T08:05:20+08:00
# weight: 1
# aliases: ["/first"]
tags: ["On-Chain Analysis", "Ethereum"]
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

```SQL
select sum(value)/10^18 as royalties, 0.7*sum(value)/10^18 as dao_royalties, 0.3*sum(value)/10^18 as artist_royalties from ethereum."transactions" tx
where tx.from = '\x0b7a434782792b539623fd72a428838ea4173b22'
and tx.to = '\x557068A9b7d66F97A61b97C80541eb17672E1e6f'
```
