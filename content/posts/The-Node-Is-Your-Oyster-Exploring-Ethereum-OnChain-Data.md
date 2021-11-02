---
title: "The Node Is Your Oyster Exploring Ethereum OnChain Data"
date: 2021-11-02T14:51:38+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Ethereum", "onChain data", "Data Exploration"]
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
Alrightttttt the node is now your oyster, now that you've connected to the Ethereum node via [Web3](https://www.dappuniversity.com/articles/web3-py-intro).

First step: What kind of data can I get from the node, and what are the methods used?

Firstly, the basics.

##### Block Number
`w3.eth.blockNumber`

##### Information of block
`w3.eth.get_block('latest')` | or: `w3.eth.get_block(13536040)`
- Base Fee Per Gas [Int]
- Difficulty
- Extra Data
- Gas Limit
- Gas Used
- Hash
- Logs Bloom
- Miner (address)
- Mix Hash
- Nounce
- Number
- Parent Hash
- Receipts Root
- Sha3 Uncles
- Size
- StateRoot
- Timestamp
- Total Difficulty
- Transactions [ Array of Tx Hash]

Out of these, only a few seem to be relevant in terms of Data analytics. TimeStamp, and transactions. Really, I can't see how the rest are that useful for what I want to explore.

I think this method is useful in general if I want to get the list of transactions within a block, that's about it.

> A quick reminder: every block on Ethereum is around 12/14 seconds. Which means that in a day, there would be 6171 blocks created.

##### Information of transaction  
`w3.eth.getTransaction('0xd695da942353697f38cc389097310de3d13aa14a18cf501196c77689705dd508')`  
- Block Hash | blockHash
- Block Number  | blockNumber
- Chain Id
- From (Address)
- Gas (amt)
- Gas Price
- Hash
- Input
- Max Fee Per Gas
- Max Priority Fee Per Gas
- To
- Nounce
- v, r, s (these fields are components of ECDSA digital signature )
- Value

Ah, the transaction data is more useful. Specifically, the following are useful:  
- from (sender address)
- to (receiver address)
- value (amount, transferred in wei)

> Okay wei is a really annoying unit to deal with. 1 Eth is a billion (10^9) Gwei. One Gwei is a billion (10^9) wei. Most of the time, you won't be counting Zeroes. Copy and paste into [this site](https://eth-converter.com/) to see the value.

Okay, that's great. These allow you to build zippy tools that simplify your life when drilling down to transaction level data. **However**, without aggregation, these data do not tell a story.

Let's see what else can Web3 do for us.

- Check if valid Eth Address:
`w3.isAddress('0x0B36748D853621251D5249c18fC18B5ab47437a1')`
- Get Address from ENS domain:
`w3.ens.address('vitalik.eth')`
- Get ENS Domain from Address:
`w3.ens.name('0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045')`
- Get Balance of Wallet (in Wei):
`w3.eth.getBalance(address)`
- Convert balance from Wei to Ether:
`w3.fromWei(1663781388884274796027, 'ether')`
- Get balance from ENS in Ether:
`w3.fromWei(w3.eth.getBalance('vitalik.eth'),'ether')`
- Get gas price now in Gwei:
`w3.fromWei(w3.eth.gas_price,'gwei')`

Alright - up to this point, the data needs to be aggregated for a picture to emerge. Which means - this is not the tool for it.

In the next post, I'll try to explore onChain Data using Dune Analytics.
