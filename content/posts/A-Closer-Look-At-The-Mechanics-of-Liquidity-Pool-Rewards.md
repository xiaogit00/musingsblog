---
title: "A Closer Look at the Mechanics of Liquidity Pool Rewards"
date: 2021-11-23T08:38:38+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Uniswap", "Liquidity Pool", "Mechanics"]
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
In my bid to understanding how APR of a liquidity pool is being calculated, I am forced to take a closer look at the mechanics of liquidity pool rewards.

Right now, all I know is that for platforms such as Uniswap, 0.3% of all swap fees will go to liquidity pool rewards, which are then distributed to liquidity providers. The rewards are distributed every block.

But there are deeper questions.
1. Does that imply that to calculate Liquidity Pool Rewards, all I need to do is to take the total volume of transaction and multiply by 0.003?

2. How do I calculate my individual portion of the pool fees?

3. Do I receive the rewards in my wallet every block? Or is it just a rolling reward on the Uniswap system, that I'll have to claim at one go eventually?

4. Most importantly: does that mean that the total rewards I'll receive in one block is a function of transaction volume? Does this then imply that the APR that I'll receive is a function of transaction volume?

5. What is the function to calculate APR from per block transaction fees?

6. Assume that the function for calculating APR is f(x). Can all the dependency variables of f(x) be found on-chain?

Lets begin my inquiry.

### Background of Uniswap
It's important to keep in mind that Uniswap is more than just an interface. The core of what Uniswap does is standardizing how ERC20s are exchanged with a set of smart contracts.

Anyone can build an interface that connects to these contracts and instantly be able to start exchanging with everyone else that is using Uniswap.

There's two types of contracts that make up Uniswap.

1. Exchange Contracts  
- hold a pool of a specific token and Ether that users can swap against.  
- for instance, this is the USDC/ETH exchange contract (https://etherscan.io/address/0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8)
- Each Pool is essentially an exchange contract that's created by the factory contract.

2. Factory Contracts  
- in charge of creating new Exchange contracts and registering the ERC20 token address to its exchange contract address.
- for instance, this is the Factory contract that created the USDC/ETH pool above (https://etherscan.io/address/0x1f98431c8ad98523631ae4a59f267346ea31f984)
- as you can see, the Factory contract has a method, createPool, which is invoked many times.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1637630591/uniswap_contract_overview_hmfxa3.png" title="" width="70%">}}

### Behind the scenes of one swap
When you make a swap on the Uniswap interface, what is happening behind the scenes is can be found on the [ERC20 Token Txns tab](https://etherscan.io/address/0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8#tokentxns) of the Exchange contract.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1637631631/Screenshot_2021-11-23_at_9.32.56_AM_s83kr0.png)

The Red boxes are examples of normal interactions with the smart contract. Each swap will trigger two events: an In and Out event.

Looking at the first example, both have the same tx hash of 0x46ad. Within this transaction, two events are happening. First, user 0xe919 inputted 4250 USDC into the USDC/ETH Exchange Contract. Then, the exchange contract sent 1.022 Eth to the Uniswap V3 Router, which presumably will send it along to 0xe919.

The same pattern repeats for the rest of the red boxes.

The Blue box shows a series of transactions done by MEV bots. The logic is the same: for instance in Rows 3 and 4, Bot inputs 97Eth into Contract, and contract outputs 400.6k USDC to Bot.

In these kinds of transactions, the contract does not send to the router, and instead deposit directly into the counterparty's address.

(An interesting question which I'll need to find out soon is: which kinds of transactions are direction I/O transaction with the contract, and which txs go through the router?)

### Listing your token
There are no listing fees to add token to Uniswap. Anyone can call a function on the Factory contract to register a new token. The function for creating pool is called `createExchange` for Uniswap v1, `createPair` for Uniswap V2, `createPool` for Uniswap V3.

To `createPool`, you input the `tokenA` and `tokenB` address, as well as the fee. Then, Factory contract checks its registry to see if an exchange contract has been created for that token address. If no Exchange address is listed, the factory contract deploys an Exchange contract and records the Exchange address in its registry.

### Fees for Uniswap
Back to my original question.

1. Does that imply that to calculate Liquidity Pool Rewards, all I need to do is to take the total volume of transaction and multiply by 0.003?
Yes, that is the case. You can ascertain that by calculating the Volume / fees ratio manually at https://info.uniswap.org/#/pools/0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8, you'll get 0.003.

2. How do I calculate my individual portion of the pool fees?
The portion you get depends on the percentage of the overall liquidity you supply. The PercentageOfTotalLiquidity in pool is unique to every LP, and constantly changing. To get a simple liquidity, if a pool consists of 50Eth, and I supplied 1Eth, then my PercentageOfTotalLiquidity will be 2% - hence, my cut of the fees every day would follow the general formula:

PersonalCutFromFees = TotalVolume * PoolFeesPercentage * PercentageOfTotalLiquidity

3. Do I receive the rewards in my wallet every block? Or is it just a rolling reward on the Uniswap system, that I'll have to claim at one go eventually?

No, you don't receive the rewards in your wallet every block. The 0.3% fee on all trades is added to the reserve pool. When you burn your pool tokens to reclaim your stake of total reserve, you receive your proportionally distributed amount of total fees accumulated while staking.

So rewards are only received when you exit your LP position.

4. Most importantly: does that mean that the total rewards I'll receive in one block is a function of transaction volume? Does this then imply that the APR that I'll receive is a function of transaction volume?

Yes it does mean that the total rewards received in the end is a function of transaction volume. Specifically, it follows this formula:

∑ (Block(i)RewardFees * 0.003 * Block(i)PercentageOfTotalLiquidity)

At each block, you have different reward fees, and you occupy a different percentage of total liquidity. Thus, your final rewards is a summation of your disparate rewards across all blocks you opened your LP position for.

5. What is the function to calculate APR from per block transaction fees?
This is interesting. If it follows that reward fees are calculated on a per block basis, then it means tht APR is also calcualted on a per block basis.

APR asks: at the current rate at which fees are being accumulated per block, what would the returns for $1 of my investment be in a year?

To answer this, we first need to ask - what are fees being paid in? I have a feeling that the fees are paid in both assets.

I've looked at the V2 smart contract and I think I can confirm this is the case. The fees are stored in the Position.sol file, and within the code there's a feeGrowthInside0LastX128 and feeGrowthInside1LastX128 is the fee growth per unit of liquidity as of the last update to fees owed. So the fees are automatically factored into the amounts of token0 and token1 that you receive when you withdraw your stake in the pool via this function.

So this brings us back to question 5. The APR figure probably leverages on the feeGrowthInside0 every block - let's call it x. ROI for block 1 = (1 + x)/ 1. Let's say it's a really small number like 0.00000000001 or something.

PercentageOfBlockFeesOverPrinciple (x) = my fees for block / principle amount

At each block, there will be a x(i).

TO get APR, we'll take `[(NumberOfBlocksInYear)(x) + 1]/1`.

Which **is also the function for calculating APR based on per block fees earned**.


6. Assume that the function for calculating APR is f(x). Can all the dependency variables of f(x) be found on-chain?

Dependencies: I would need the  feeGrowthInside variable.

Time series of X (percentageOfBlockFeesOverPrinciple)
The Pool APR is actually the realized pool fees 0.003 * (NumberOfBlocksInYear) = 0.003*2294755 = 6884. Okay this doesn't really make sense. I don't think this formula is correct. 
