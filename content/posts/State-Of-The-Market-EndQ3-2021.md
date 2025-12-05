---
title: "State of our Layer 2s - (Oct 2021)"
date: 2021-10-26T18:38:56+08:00
# weight: 1
# aliases: ["/first"]
tags: ["layer 2", "scalability","ethereum"]
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
    image: "https://res.cloudinary.com/dl4murstw/image/upload/v1635481646/Layer-1_and_Layer-2_Blockchain_Scaling_Solutions-100_esrobn.webp" # image path/url
    alt: "<alt text>" # alt text
    caption: "Layer two solutions - the answer to Ethereum's gas and TPS bottleneck problem. (Image: Gemini)" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
It's Q4 of 2021 (28th Oct), and Ethereum's gas fees are currently raging at $17USD / transaction. If you catch the right, off-peak hours such as Saturday evenings GMT +8, you could get it cheaper at $2.30USD. The problem is: will your profitable trade wait till Saturday evenings? This brings us back to our original problem. What is the current state of development of our Layer 2 solutions?

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635648966/Screenshot_2021-10-31_at_10.55.10_AM_c7z6it.png" title="Layer 2 solutions manifesting on Uniswap as network options (31 Oct 2021)" >}}

>## TLDR:
> - Arbitrum is the only successful implementation of L2 solution at scale.
> - Other projects, such as Optimism, and zkSync, are either operating at curtailed scale, or are still in development/testing
> - Gas fees are still not ridiculously cheap for now. If broke, use Polygon.

Let us first begin by considering Polygon. In my previous [post two months ago (13 Aug 21)](https://www.musingsondefi.xyz/posts/polygon-chain-review/), we saw that Polygon was the third largest network with 5.66Bn TLV. It's widely heralded as a low cost alternative to Eth, with transaction costs going down to $0.00005USD and a TPS of ~88. Since then, much has changed. Not only has other projects such as Solana, Terra, Avalanche, and Fantom overtaken it in terms of TVL, Polygon's TVL has actually *declined* over the period.

The specific cause of Polygon's decline is a topic reserved for another post. For now, let's turn our attention to the state of development of Layer 2 solutions.

As of Oct 2021, different layer two solutions are at different stages of development.

### Arbitrum leads the charge

The most successful implementation of layer 2 solution at scale is Arbitrum. Arbitrum lies in the Optimistic class of rollup solutions. Since rolling out its mainnet, Arbitrum One, on the 31st August, Arbitrum's TVL quickly rose to $2.26Bn (27th Oct 21) within a short span of 2 months.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635482178/Screenshot_2021-10-27_at_7.10.34_PM_tandpp.png" title="Arbitrum TVL (Source: DefiLlama)" >}}

At the time of writing, Polygon's TVL is currently standing at $4.6Bn. For a 2 months old project, Arbitrum has already captured half the value share of its competitor Polygon.

To bring in another context, Arbitrum is currently at 1.4% of Etereum mainnet's TVL (159.27Bn[^1])

To date, there are 83 dApps live in the Arbitrum ecosystem. These include wallets, dApps, tools, and bridges. 37 of these projects contribute to its $2.26Bn TVL, with Curve leading the way at a 27.89% dominance, followed by SushiSwap, AnySwap, and Abracadabra.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635483209/Screenshot_2021-10-29_at_12.53.22_PM_v8jhwa.png" title="Top TVL contributing protocols on Arbitrum One (Source: DefiLlama, 27th Oct)" >}}

However, Arbitrum is still far from perfect. Its TPS is currently still around 15, and fees are roughly $2 - $5USD / transaction. Compared to Ethreum mainnet's $17, it is much cheaper, but it cannot reach the dirt cheap levels like Polygon or Solana, making it unsuitable for microtransactions. According to [one redditor](https://www.reddit.com/r/Arbitrum/comments/qb7vof/what_is_the_possible_lowest_fee_on_arbitrum/), the situation will likely improve in the future as Arbitrum is currently still in beta and the transaction volume is artifically capped, leading to increased prices. When the Arbitrum team is ready in 3-6 months to expand capacity, gas prices would fall.

### Interlude: Effects on Polygon?

One interesting point of observation, as mentioned earlier, is the correlation between the launch of Arbitrum and the stagnation of Polygon's TVL - something that is not surprising but worth pointing out. On the day of Arbitrum's launch, Polygon's TVL stands at $5.54Bn. Today, it stands at $4.77Bn.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635483418/Screenshot_2021-10-29_at_12.56.50_PM_wt4uia.png" title="Polygon TVL on 31st Aug" >}}


When the TLV of Ethereum rallied from 11 Sep, Polygon's TLV did not climb.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635483607/Screenshot_2021-10-27_at_7.03.25_PM_g45vke.png" title="Eth Rally" >}}


{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1635483611/Screenshot_2021-10-27_at_7.04.27_PM_tdheik.png" title="Polygon TVL in comparison"  >}}

Polygon, essentially, *did not grow* in terms of TVL from 11 Sep. Of course, we should not infer causation here. Further research should be done on the cause of Polygon's stagnation. But one very plausible reason is due to Arbitrum (or other Layer 2) draining all the value away from it.

#### Overall picture for Arbitrum
Arbitrum is doing exceedingly well. Its TVL has been climbing rapidly and the rate of adoption has been exponential. Crypto communities, it seems, have embraced it with open arms. The gas prices are not dirt cheap for now, but are not exorbitant either. Importantly, it would become cheaper in time. The TPS for now is still around 15, which does not solve Ethereum's scaling problem. Once the trial period is over, the full powers of Arbitrum can then be unleashed (purpotedly) with a drastically increased TPS - something for us to await.

### Other Layer 2 Solutions
On the other hand, in the Optimistic rollup family, we have Optimism - a project that is ever-delayed but promises great potential. As of today, most of the projects on Optimism are still doing their integration testing on their Kovan testnet. Some projects, however, like Synthetix and Uniswap, have already done their mainnet alpha launch in July. *This means that only a couple projects on Optimism network is in production, whilst most of the rest are not.* This differs greatly with the progress of Arbitrum, where a majority are in production. Till date, Optimism's TVL is $146.65m, with 4 dApps taking up 100% of the TVL: Synthetix ($88m), Uniswap V3 ($34m), Hop protocol ($10m), and Lyra ($6m). In general, Optimism team is deciding to take a slower and more careful approach with their launch, choosing the whitelist approach instead of an open approach.

One key upcoming feature of Optimism is its anticipated mainnet EVM equivalent upgrade on 11Nov. This upgrade would allow EVM compatible dApps to run one-click deployment on Optimism layer 2 - something that could potentially give them moat over Arbitrum.

Now, let’s take a look at how things are doing on the zkRollups side.

Overall, most zkRollup solutions are still in development/testing, but there are specific projects leveraging on zkRollup tech in production with promising signs of success.

### zkSync
So far, the big player in the game, zkSync, has been taking it slow. No dApps are currently being deployed to production - although testnet is already open in Phase 1, with Curve having already deployed a working testnet integration there. There is no set date yet for their Mainnet fair launch. One big developmental breakthrough for the zkSync team occurred on 13 Oct, where they successfully ported over Uniswap V2 into zkSync using the zkEVM. This is significant because zkEVM would allow for easier porting over of current dApps into zkSync ecosystem.

Apart from zkSync, there are also other, more specifically taylored projects based on zkRollup technology that are already in production. These projects have timelines and ambitions of their own, and their route to market entry is also different.

First up we have Starkware. On [l2beat](https://l2beat.com/), we see that the biggest L2 project in terms of TVL is dYdX. Underneath the hood, dYdX's perpetuals trading platform is built using StarkWares' StarkEx scalability engine. In terms of actual successful implementation of zkRollup solution at scale, StarkWare's solution is currently coming up top, capturing $1.03Bn of TVL with the help of dYdX.

Coming up second we have Loopring, a zkRollup solution built specifically for the context of trading. According to L2Beat, Loopring currently has $116TVL, although 44% of it can be attributed to their LoopRing LRC token. They don't seem to have similar ambitions such as zkSync to expand into a general purpose zkRollup solution for dApps, instead it is a production version built specifically for their Loopring trading platform.

### Conclusion
So, what can we say about the current state of affairs of L2 solutions? The overall picture is that we are still not quite there yet in terms of reaching the full potentials of L2 (benchmarked by low fees and high throughput); however, various projects, such as Arbitrum, are showing us promising signs of success. We're, in other words, **battle testing** our layer two solutions. This is of course a crucial part in building sound architecture.  

On the side of the market, if you're an average trader looking to have consistently $2 fees, then apps in the Arbitrum ecosystem are your answer. They’re not dirt cheap, but they promise Ethereum level security.

Optimism also gets the job done too, although they provide a much more limited suite of Apps. Big names like Uniswap can be found on it.

For microtransactions and people who are cost-conscious, Polygon is still the way to go. Security wise, the general perception is that it doesn’t have the rigourous standards of Ethereum Rollups - but I have not looked in-depth into this issue.

### Looking ahead:

What’s exciting and in the pipeline are when solutions like zkEVM, and Optimism EVM upgrades launch amongst developers and see mass deployment of dApps.

It’s safe to say that in the foreseeable future, gas fees can be reduced by drastic proportions given these new technologies and dApps running on these layers twos.

Next check-in date: 20th Dec 2021.




[^1]: For the sake of consistency, all figures used henceforth (unless stated otherwise) refer to the figures for 27th Oct 2021.
