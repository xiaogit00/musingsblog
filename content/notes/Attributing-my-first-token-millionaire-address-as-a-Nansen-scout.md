---
title: "Attributing My First Token Millionaire Address (0x5cda) as a Nansen Scout"
date: 2021-12-22T12:36:25+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
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

Background: I've enrolled into the Nansen Scout program, which is a really interesting program where you can attribute addresses in exchange for rewards.

In this post, I'll be detailing my journey into attributing a 'token millionaire' address 0x5cdaf83e077dbac2692b5864ca18b61d67453be8.


---

<!-- Nansen exposes its scouts to its retool UI, which is basically an internal UI that allows external scouts to input their findings. A little like a more advanced version of Google sheets - its a data startup after all.

All scouts are given a to-do list. On my to-do list, there is a very juicy Token Millionaire address with value of over 10 million - a prime target for my detective work.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640148227/Screenshot_2021-12-22_at_12.42.04_PM_1_bkbf6m.png) -->

## First steps
First, I entered the address into Sanbase historical balance to get a sense of asset distribution.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640148592/Screenshot_2021-12-22_at_12.49.46_PM_twwhzt.png)

#### We observe that the wallet holds $6.1M USDT, and $3.8M in ZKS.

Digging further, we find that:

#### The wallet was created 6 days ago, and has a lot of transactions (roughly 50%) that deals with ZKS.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640148890/Screenshot_2021-12-22_at_12.52.57_PM_isrglr.png)

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640148891/Screenshot_2021-12-22_at_12.52.50_PM_gvxewq.png)

There is one particular transaction that catches our attention.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640149002/Screenshot_2021-12-22_at_12.56.10_PM_zlvgki.png)
>Etherscan: https://etherscan.io/tx/0x303fba30793e2f55e38e0ca24801c72bdca12da425311b5a18791bdf4b399c94

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640149189/Screenshot_2021-12-22_at_12.58.30_PM_kiahww.png)

Within this transaction, we find that:

#### 8.5M ZKS tokens are transferred from *0xf786792b565a3ddf50e6f435595936070cd62755* to our Token millionaire, with a value of $3.49M, on 21st Dec, 9:55AM UTC.

> We shall note this address *0xf786792b565a3ddf50e6f435595936070cd62755* -(0xf786) - temporarily as *Transferrer of Huge 8.4M ZKS tokens to Token Millionaire*.

Now, there's a few ways we could roll with this.

1. Check out the price of ZKS, esp on 21st Dec.
2. Project details of ZKS - identify team, developers, investors, advisors. Identify whether there's private token launch recently.
3. Identify ZKS contract
4. Identify big transfers from ZKS contract

## Recent Price of ZKS

### Price since launch

#### ZKS token was launched on Jan 7th 2021 to a price of $0.29.
![](https://res.cloudinary.com/dl4murstw/image/upload/v1640150906/Screenshot_2021-12-22_at_1.25.45_PM_ujelqr.png)


#### Its price peaked to $9.89 on the 22th Feb 2021, and $9.65 on 25th Feb 2021...
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640151128/Screenshot_2021-12-22_at_1.30.20_PM_zcojkj.png" width="50%" >}}
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640151127/Screenshot_2021-12-22_at_1.30.27_PM_jnso3v.png" width="50%" >}}

#### before falling drastically to $4.33 on the same day (25th).

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640151127/Screenshot_2021-12-22_at_1.30.48_PM_gy5vdl.png" width="50%" >}}

> Mental note: we could check out the reason for this price action.

For now, lets take a look at the recent price action.

### Recent month price
![](https://res.cloudinary.com/dl4murstw/image/upload/v1640151565/Screenshot_2021-12-22_at_1.39.09_PM_l6b4z9.png)
Looking at the price for the last month, we find that there's no sudden changes in price. Price has been hovering around $0.4, and not moved much since.

On the 21st, at the time 8.5m ZKS is moved to our Token Millionaire, price is relatively stable.

## Project Details of ZKS

Without going into the details, we first need to find out the circulating supply of ZKS. Based on [coin98insights](https://coin98insights.com/what-is-zkswap-zks#ZKS_Token_Allocation),

#### ZKS has a max supply of 1Bn tokens, out of which its currently circulating supply is 223,450,000 ZKS.

This makes a the 8.5M transfer 3.8% of the total circulating supply.

Based on their whitepaper, they have the following token allocations and schedule:

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640152341/Screenshot_2021-12-22_at_1.51.03_PM_y77t93.png" width="60%"  >}}

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640152728/Screenshot_2021-12-22_at_1.57.56_PM_x1dvb6.png" title="" width="60%"  >}}

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640156088/Screenshot_2021-12-22_at_2.54.38_PM_jlelfl.png" title="" width="60%"  >}}

If we take 7th Jan to be the launch date of the token, then we are nearing the second year of the token launch. The 8.5M token transfer could be a standard token distribution to any of the following parties:
- ZKSwap Team
- Angel Investors
- Potential Round A investors.
- Advisors
- Ecosystem Developers

Based on [Huobi Global](https://huobiglobal.zendesk.com/hc/en-us/articles/900004502746-ZKS-ZKSwap-Introduction), the following is the token lockup and release plan:

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640153259/Screenshot_2021-12-22_at_2.07.30_PM_h2k09r.png" title="" width="70%"  >}}

### Address 0xf786

Next, we shall look at the address that transferred 8.5m ZKS token to our token millionaire.

First, we find that 0xf78 has made a total of 99 ZKS transactions in 2021, with the first one being 5th Jan 2021.

The first [ZKS transaction](https://etherscan.io/tx/0x1c18226ff9e447c1c00231e24a01e0706ae7816895e65e7a05c69cb178de6a80) saw `0xf78` receive 20m ZKS tokens, from `0x39bd`.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640155189/Screenshot_2021-12-22_at_2.39.17_PM_pbz0xy.png)

We can only safely conclude for now that **`0xf786` is an extremely high transactor of ZKS. He has made 35 transfers of ZKS that are over a million in value, 52 that are over $100,000.**

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640155617/Screenshot_2021-12-22_at_2.46.47_PM_psdkji.png)

### Address `0x39bd`

On 5th Jan 2021, `0x39bd` transferred 20M ZKS tokens to `0xf78`.

20M is 2% of total circulating supply, and 8% of current total circulating supply (223M).

If we do the math, the first year is supposed to have a circulating supply of 33.03% of 1Bn - which is 330M tokens.

20M of 330M is 6.06%.

Looking at our chart earlier,

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640152341/Screenshot_2021-12-22_at_1.51.03_PM_y77t93.png" width="60%"  >}}

**6.06% corresponds to the portion allocated to angel investors for the first year**

### There is a high chance that `0xf78` is the wallet used to distribute token allocation to angel investors.

What, then, is `0x39bd`?

Looking at [etherscan](https://etherscan.io/address/0x39bdb7cfdfdf7e3425166210fd8569d8ee037124#tokentxns), it is obvious that

### `0x39bd` belongs to ZKS team - might be a cold wallet - that distributes funds in bulk to various classes of token allocation recipients.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640156575/Screenshot_2021-12-22_at_3.02.45_PM_tyrcaa.png)

From here, we can conclude a few things.

### On Nov 22 2020, the ZKSwap Deployer (0x6383) transferred 1Bn (entire circulating supply of ZKS) to 0x39bd.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640157097/Screenshot_2021-12-22_at_3.02.45_PM_tyrcaa_x0lyxy.png)

Subsequently, 0x39bd was responsible for distributing funds to a host of smaller wallets, of which 0xf78 received 20M on 5th Jan 2021.

> ### What do we know for certain at this point? First, 0x38bd was directly used to distribute the entire circulating supply of 1Bn tokens to 7 unique addresses. This can only be done by ZKSwap.

The amounts distributed to each of those 7 addresses corresponds to the token allocation plan:

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1640152341/Screenshot_2021-12-22_at_1.51.03_PM_y77t93.png" width="60%"  >}}

Matching each token allocation, we get the following:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640176478/Screenshot_2021-12-22_at_3.02.45_PM_tyrcaa_hwhny7.png)

First, 600M is distributed to Community Mining. 150M set aside for ZKSwap team. 67M corresponds to Angel Investors, and 53M corresponds to Round A investors. 0x43bf is a slightly strange one - 100M was distributed initially, but 10M was then sent back. It holds 90M, which can be accounted for by Ecosystem Developers and Advisors. Finally, there's 40M distributed across two addresses, that are for Initial Liquidity.

Our address of interest 0xf78 belongs to the Initial Liquidity pool. I shall call it Initial Liquidity Settlement account temporarily. A cursory glance at the kinds of transactions on this account shows that much of it interacts with DEXs and CEXs, which supports this theory.

Let's take a look at how this Initial Liquidity Settlement account interacts with our Token Millionaire.


![](https://res.cloudinary.com/dl4murstw/image/upload/v1640167340/Screenshot_2021-12-22_at_5.56.42_PM_ul6xfw.png)

Filtering the transactions between Initial Liquidity Settlement and Token Millionaire, we see that our Token Millionaire is a recipient of several large transactions from Initial Liquidity Settlement Wallet. $6M USDT, ~$117k BTC, 8.5M ZKS token, 4.9k UNI token, etc.

Our initial hunch is to suspect that, given the transfers are from Initial Liquidity Settlement fund, then the purpose of the Token Millionaire wallet is for conducting liquidity related tasks within DEXs or CEXs. It could be a managed fund in some ways, ran by someone.

We'll need to confirm our hypothesis by examining wallet activity of token millionaire.

What did Token Millionaire do with the USDT, ZKS, and BTC, for instance? What has the wallet been used for?

### Examining OxCDAF
There are a few things that I clearly missed about 0xCDAF.

#### First, OxCDAF is not a token millionaire. It is a proxy contract.

The reason is because, the contract name is 'Proxy', and the code is exactly the same as the ZKSwap V2 Bridge contract.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1640169292/Screenshot_2021-12-22_at_6.34.44_PM_doe0mu.png)

#### Therefore, the right label for this contract is ZKSwap V2 Bridge Proxy Contract.

Okay. I should have seen this earlier. Mystery solved. There's leads for the whole family of addresses that I'll eventually input. 
