---
title: "Exploring Lens"
date: 2022-12-20T06:29:43+08:00
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

So I have 2 weeks of break before the new year, and one of the things that I plan to do is to explore some of the web3 protocols that I have been sleeping on. 

Lens ranks pretty high on my list.

(And this is not just because I hate instagram, for an assortment of reasons.)

So what exactly is Lens? 

For those of you who might not know, Lens is this pretty hype 'web3' social media platform that's been trending in the last few months. 

They are still in closed beta, and definitely not ready for public access yet. 

Their fundamental premise is simple. Whilst the likes of Facebook offer us a free service in exchange for ownership of our data, platforms like Lens promise that our data is always public and available to us, whilst providing certain idiosyncratically web3 like experiences. And yes, all the information is stored on the blockchain. 

What does this actually mean?

Well, in effect, this means that when you create a 'profile', you're granted a 'profile nft' which acts as the gateway to your 'account'. All your *social media actions*, like following someone, or commenting on someone's post, or reposting, are essentially transactions against the blockchain, and most of them, again, are stored as NFTs. 

As David Silverman, Product Manager at Lens, himself has said, the Lens protocol 'revolves around an NFT infrastructure layer'. 
[Lens NFT Centric](https://res.cloudinary.com/dl4murstw/image/upload/v1671490994/Screenshot_2022-12-20_at_6.58.13_AM_cjhayc.png)

This allows, amongst other things, for this one fundamentally idiocratic property: the 'backend' social data can be transported into whatever 'frontend' you want to render it on. 

If this sounds familiar to you, then it's probably not surprising: it mirrors exactly the experience of using an API. Web3 social media, is essentially 'headless' CMS powered by blockchains. 

Alright, enough about the background - let's dive into the code and see what they have to offer for developers. 

### The Lens Developer quickstart tutorial

I completed the [developer quickstart tutorial](https://docs.lens.xyz/docs/developer-quickstart) last night, and realized that a big part of reading data from Lens backend is essentially reading from their graphql API. 

And to interact with the database, you're sending a *mutation* query to the GraphQL server. 

And...well, that's about it really. 

Of course, there's quite a few parts on the [smart contract side](https://docs.lens.xyz/docs/deployed-contract-addresses) that enables all these functionalities, but as the end user, I don't *really* have to care about that. 


### The Lens ecosystem

Looking at some of the top apps in the Lens ecosystem, I find lenster.xyz. The UI is fairly decent, and it seems like there's *some* level of activity on it. The only problem right now is that you have to be logged in with a Lens profile in order to use it. 

So I can't really do most of those interesting actions, like following, likes, or repost, or so on. 

But that is fine. 

The really unique aspect of Lenster, is not this site *per se*. It's the fact that all your data will be populated to another site, another UI. 

This is the Vitalik profile on Lenster:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1671497659/Screenshot_2022-12-20_at_8.53.41_AM_rae7sg.png)

Whereas this is his profile on Orb, the mobile version:

![](https://res.cloudinary.com/dl4murstw/image/upload/v1671498103/photo1671497980_itbals.jpg)

This makes the 'skin', just a skin, without moat. 

Well, there is definitely moat - the best UI with the highest network effect wins, but does not have monopoly to that data. The barriers to entry is also significantly lower, making it an interesting kind of environment. 

**Orb App**
The Orb app is perhaps one of the more interesting finds. It features a 'feed', a simple UI, this thing called 'communities' which is a token gated community feature, and a learn section. A pretty promising app. 

**Lenstube**

What I've also discovered is this site called Lenstube. It's literally Youtube, but Web3. 

In this 'about' page, Lenstube says that the content is uploaded to Arweave, and that users can post their video content and monetize their content. Viewers can also support users by minting their favourite content, and tips. 

Okay. Let's take a step back and think about what really differentiates this from Youtube. Youtube, the god of the video world, has giant network effects, both in terms of audience, as well as the diversity and originality of content. At this stage, any new entrants will suffer from the lack of traction primarily from the content side. Content creators need to have a great incentive to be on Lenstube. Tiktok solved this problem by acquiring content creators by the boat load for many years. 

Second, the two features that differentiates this from Youtube is the ability for you to tip the creators of a video, and mint their works as NFTs. This provides a direct form of monetization, which allows Lenstube to remain ad free. This direct monetization channel is a powerful one that Web3 enables, but there's headwinds: 

1. First, it assumes that your users will be crypto natives - that everyday aunties and grandmas would even have a metamask wallet to begin with, to know how to onramp it with cash, to have the incentive to do so in the first place, and to be able to produce those sweet incentives at scale for content creators. 

2. Second, there needs to be a broader cultural shift towards *tipping*, which is an unknown at this point. Why should one tip when you can get the content for free? This, however, has a higher probability to change than the first. 

3. Third, the minting as NFT channel only really works if people buy it. At this stage, it's again an unknown. What incentives would people have to own a video NFT of 'Snowy Mountains in the Desert'? 

4. There is also a deeper issue with copyright. Some of those videos are simply short clips taken from somewhere else on the internet, sliced and uploaded into Lenstube - and there will be way more of those. 

Going by the 'no more than 1 miracle rule', all these do seem like fairly large miracles. The biggest miracle is people migrating from Youtube to this. The second biggest miracle is onboarding normies into Web3. I can see how NFTs can be attractive if we provide things like token gated content, or exclusive content for owners of certain video NFTs. 

Perhaps what this platform is should not primarily be a 'mass audience' platform like Youtube, but a 'video art' platform like Vimeo. 

Vimeo, is by nature, more 'tasteful', with longer content, and also more curated. The tipping and NFT functionalities also fits into the value and circulation economy of artistic content. 

Anyyyway. All these are speculatory. The gist of this is, there's a lot of miracles needed for Lenstube to fly, given that it's just an inferior Youtube with tipping functionalities and a *Connect A Wallet* button. Those are not compelling reasons for people to get on the platform. In fact, these are anti-reasons. 

---

I've taken a long digression into Lenstube. The gist of it is, 'web3 social' is still pretty much an ongoing experiment with the limits and idiosyncracies of this technology. There is definitely no product market fit yet, and it's unclear what the 'pull factor' is for mainstream society outside of the group of its core proponents. 

Because if there's anything we've learned about the average person, is that they'd very gladly trade away their data ownership and privacy, for good UX, no cost, and convenience. 