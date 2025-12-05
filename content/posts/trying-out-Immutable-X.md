---
title: "Trying Out Immutable-X - a layer 2 NFT marketplace powered by StarkEx"
date: 2022-06-18T13:20:12+08:00
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



So recently I've stumbled upon this new NFT marketplace called Immutable X, which apparently features gas free minting and positions itself to be the future of web3 games. It piqued my interest, and being the brave web3 voyager that I am, I've decided to give it a try. (I've been exploring new tech in this space recently, partly to better my understanding of what is currently possible, and partly because I am curious about the state of Layer 2).


So going to Immutable-X's main page, it seems like they do have an NFT marketplace, which looks like this:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1655529986/Screenshot_2022-06-18_at_1.25.30_PM_iptrjd.png)

What I am immediately most curious about is how they connect to your wallet. Given that they brand themselves as a 'layer 2' solution, does it involve changing chainIDs on Metamask like how you do with Arbitrum or Optimism?

So here's how you're 'connected' to Layer 2 through Immutable X.

First, you connect to Ethereum Layer 1 with metamask. Next, you deposit some ETH to the Immutable X contract, which will bridge your funds to Layer 2 in the process.

I've decided to deposit 0.1Eth just to test it out. Looking at the [txn](https://etherscan.io/tx/0xfae5088c28943996d5ed6aed3cde2f87cf658f55e8042fcdc34a33e45142e44a) on Etherscan, we notice a few things.

First, the transaction is an interaction with the [Immutable X: Registration](https://etherscan.io/address/0x72a06bf2a1ce5e39cba06c0cab824960b587d64c) contract. The function that we are calling here is the `registerAndDeposit()` function.

This is the function in solidity:
``` javascript
function registerAndDeposit(
        address ethKey,
        uint256 starkKey,
        bytes calldata signature,
        uint256 assetType,
        uint256 vaultId,
        uint256 quantizedAmount
    ) external {
        imx.registerUser(ethKey, starkKey, signature);
        imx.deposit(starkKey, assetType, vaultId, quantizedAmount);
    }

```

Then, this function makes a call to the Immutable X [Bridge contract](https://etherscan.io/address/0x5fdcca53617f4d2b9134b29090c87d01058e27e9) with the value that I've deposited. Here, I see that it actually emits two events from the bridge contract, but I don't exactly know which are the two functions that are called because the log events are [not decoded](https://etherscan.io/tx/0xfae5088c28943996d5ed6aed3cde2f87cf658f55e8042fcdc34a33e45142e44a#eventlog).

In any case, the long story short is, when you're depositing your mainnet ETH into ImmutableX, you're basically interacting with the IMX contract first, registering for a starkkey, and then depositing your Eth into the Immutable X Bridge contract. The latter contract is the so called 'layer 2' solution that is written and operated by Starkware.

Very interesting.

### Buying an NFT

So I've decided to further my journey by buying an NFT and seeing what kind of on-chain interactions take place.

This one really screamed out to me for no reason:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1655533169/Screenshot_2022-06-18_at_2.19.05_PM_ezrwab.png)

The way it stares at me, I just can't resist. And it's just 5 bucks. Let's do it.

### On-chain interactions

When I clicked confirm purchase, the transaction was instantly finalized. I find that nothing is reflected on my metamask. However, on immutable X landing page, under the transactions tab, it says here that I've made a purchase. Checking out my transaction ID on Immutascan:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1655533937/Screenshot_2022-06-18_at_2.29.47_PM_kwbvub.png)

Now I know what's going on. When I make a transaction via Immutable X, the on-chain interaction happens on Starkware's layer 2 engine. This engine runs on their cloud servers and batches all the transactions before relaying them to Layer 1.  

All the 'On-chain' data is therefore only accessible on Layer 2 - in this case, via the immutablescan UI. Nothing will be reflected on layer 1.

### Conclusion

At the start of this post, I wanted to find out what the experience of using Layer 2 is. My verdict is this: it feels very much like I am using Polygon. The transactions are negligible in cost, they're fast, and really, on the side of the user, there's not much difference. 
