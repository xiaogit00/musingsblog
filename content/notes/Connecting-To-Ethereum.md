---
title: "Connecting to Ethereum"
date: 2021-11-02T14:34:06+08:00
# weight: 1
# aliases: ["/first"]
tags: ["ethereum", "web3"]
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
These are the main concepts regarding programmatic connections to Ethereum.

You use Infura to access Ethereum node via API.

### Step 1: Sign up on Infura and get the API endpoint.
'https://mainnet.infura.io/v3/397e0ef1xxx'

### Step 2: Connect to Web3
```python
from web3 import Web3
infura_url='https://mainnet.infura.io/v3/397e0ef1b1dd4f6e85b10c3936724da3'
w3 = Web3(Web3.HTTPProvider(infura_url))
w3.isConnected()
```

That's it! You're connected to an Ethereum node. 
