---
title: "A Close Comparison Between Our Nft Endpoints and Simple Hash"
date: 2022-06-03T09:16:31+08:00
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
## Introduction to the problem
As Erik mentioned, a popular wallet service like Rainbow finds that simplehash has the best NFT data. In this post, we'll do a deep comparison between the services that we offer versus what SimpleHash offers, and try to answer the classic Dev rel question: as a developer, which would I recommend to my developer friend? If the answer is simpleHash, we've lost the competition.

Specifically, we'll examine each of Rainbow wallet's feedback to us in details:
- Our NFT endpoints time out
- We don't have a good renderer for the SVGs
- Our coverage is good, but since there are gaps in the data it's a little frustrating. Need something very consistent
- Don't want to use someone else, but these guys work very well
- These guys cover both SVG's don't time out, and have multi-chain support outside of ETH"

## Comparison of the scope of data provision

Covalent has 4 main NFT endpoints.
- `Get NFT Global Market View`
- `Get NFT Token IDs for contract`
- `Get NFT Transactions for contract`
- `Get NFT External metadata for contract`

SimpleHash has 9 NFT related endpoints:
- `NFT by Token ID`
- `NFTs by Contract`
- `NFTs by Wallet(s)`
- `All NFTs`
- `Transfers by NFT`
- `Owners by NFT`
- `Owners by Contract`
- `Ownership Summary by Wallet(s)`
- `NFT Collections`

Let's take a quick look at what each of these endpoints return.

`NFT by Token ID` **returns us the metadata of each NFT token**. This is not the same as our `Get NFT Token IDs for Contract`. It is useful for pulling, in our terminology, the 'metadata' of each individual NFT. 

``` json
{
  "chain": "ethereum",
  "contract_address": "0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D",
  "token_id": "100",
  "name": null,
  "description": null,
  "image_url": "https://cdn.simplehash.com/assets/1512741aea29a0e4e9da38afd23e2bc1d5748d0887d118b6e351d43dcf27c8fd.png",
  "video_url": null,
  "audio_url": null,
  "model_url": null,
  "previews": {
    "image_small_url": "https://lh3.googleusercontent.com/MeRV37VY4cvyri-rfWGgwaDtQGaoT_51vh7z-DEIArvS22AGvJ2MKfWJHK_-DMIJMgX3Wy24YpABI8d3QxVpmrLvqZlVwhHibys=s250",
    "image_medium_url": "https://lh3.googleusercontent.com/MeRV37VY4cvyri-rfWGgwaDtQGaoT_51vh7z-DEIArvS22AGvJ2MKfWJHK_-DMIJMgX3Wy24YpABI8d3QxVpmrLvqZlVwhHibys",
    "image_large_url": "https://lh3.googleusercontent.com/MeRV37VY4cvyri-rfWGgwaDtQGaoT_51vh7z-DEIArvS22AGvJ2MKfWJHK_-DMIJMgX3Wy24YpABI8d3QxVpmrLvqZlVwhHibys=s1000",
    "image_opengraph_url": "https://lh3.googleusercontent.com/MeRV37VY4cvyri-rfWGgwaDtQGaoT_51vh7z-DEIArvS22AGvJ2MKfWJHK_-DMIJMgX3Wy24YpABI8d3QxVpmrLvqZlVwhHibys=k-w1200-s2400-rj",
    "blurhash": "UaOM[yRk_Jxt%Mt6M{WBX5RkV[ox%KkBRln+"
  },
  "background_color": null,
  "external_url": null,
  "extra_metadata": {
    "attributes": [
      {
        "trait_type": "Background",
        "value": "Yellow"
      },
      {
        "trait_type": "Mouth",
        "value": "Bored Cigarette"
      },
      {
        "trait_type": "Hat",
        "value": "Party Hat 2"
      },
      {
        "trait_type": "Fur",
        "value": "Dark Brown"
      },
      {
        "trait_type": "Eyes",
        "value": "Wide Eyed"
      }
    ],
    "image_original_url": "ipfs://QmTXuAUW4nLqRZ8NCbEaH8tPSYVhqq3hPTYbnqx8p97Yi7",
    "animation_original_url": null
  },
  "created_date": "2021-04-24T10:21:27",
  "status": "minted",
  "token_count": 1,
  "owner_count": 1,
  "owners": [
    {
      "owner_address": "0x50FaFf505981949e2bAb7Ae447a9E5DBf0Ec3a92",
      "quantity": 1,
      "first_acquired_date": "2022-05-06T09:54:31",
      "last_acquired_date": "2022-05-06T09:54:31"
    }
  ],
  "contract": {
    "type": "ERC721",
    "name": "BoredApeYachtClub"
  },
  "collection": {
    "name": "Bored Ape Yacht Club",
    "description": "The Bored Ape Yacht Club is a collection of 10,000 unique Bored Ape NFTs— unique digital collectibles living on the Ethereum blockchain. Your Bored Ape doubles as your Yacht Club membership card, and grants access to members-only benefits, the first of which is access to THE BATHROOM, a collaborative graffiti board. Future areas and perks can be unlocked by the community through roadmap activation. Visit www.BoredApeYachtClub.com for more details.",
    "image_url": "https://lh3.googleusercontent.com/nV9f6T3m02NpTtJif5p9ZmqiS_yWzBfe9kAqY2Elnchd4UPiXf1M-NBO_g0Sk76Akn0FtVzB69LmieW7yvJN5SVsrnz7n2-Fc6Y",
    "banner_image_url": "https://lh3.googleusercontent.com/wZE_J5z23GgLu9CBHWPWGbhMuWGY_XebJkjE_yGMZiVDFJ_DMcJWYMZ1GKNZ01KHCSyZZjMWzR6BEP0Y3XUM4Crj6r4Mdod9Y55s=w2500",
    "external_url": "http://www.boredapeyachtclub.com/",
    "twitter_username": "BoredApeYC",
    "discord_url": "https://discord.gg/3P5K3dzgdB",
    "marketplace_pages": [
      {
        "marketplace_name": "OpenSea",
        "collection_url": "https://opensea.io/collection/boredapeyachtclub",
        "verified": true
      }
    ]
  }
}
```
---

### SimpleHash's naming is more intuitive.
Just from the naming of the NFT endpoints themselves, SimpleHash's naming seems to be more intuitive for the layman.

The key perspective to adopt here is: Without knowing anything the underlying data structure (or necessarily about NFTs much), what can we deduce about the *use value* of each of these endpoints?

2 out of 4 of *our* endpoints do not have immediately decipherable use values: `Get NFT Global Market View`, and `Get External Metadata for contract`. Try this exercise. Ask a new developer friend, what kind of data do you think is returned from these two endpoints? If they can guess the category of data returned, then the endpoint can be said to be *intuitively named*. Intuitive naming conventions aids greatly the data discovery and onboarding process - and even in everyday usage, as it takes up less mental gas for developers. I.e, it greatly increasing developer QOL (quality of life). **Improving developer QOL is key to retention and his willingness to pay or even spread the word**.

<!-- A more intuitive name for the first one could be: Top NFT Rankings by Market Cap. -->
In any case, let's move on away from all the theory. We've explored how Simplehash's naming conventions seem to be more intuitive, but I want to dive deeper into why that's the case. It turns out, that:

## There is clear separation of concerns in that each endpoint answers a single question that a developer/user wants to know.

For instance, these are the common questions that someone interested in NFTs would want to know:  
- What more can I know about this one token?  
- What can I know about this NFT collection?  
- What NFTs does this Wallet own?  
- How many transfers did this NFT have?  
- Who are the list of owners of this NFT?  

__________________________
| SimpleHash        | Covalent    |
| ------------- |:-------------:|
| `NFT by Token ID`         | `Get NFT External metadata for contract`  |
| `NFTs by Contract`        |   `Get NFT Token IDs for contract` +  `Get NFT External metadata for contract`  |
| `NFTs by Wallet(s)`       | `balances`      |
| `All NFTs`                |        na      |
| `Transfers by NFT`        |   `Get NFT Transactions for contract`    |
| `Owners by NFT`           |    na   |
| `Owners by Contract`      |    na   |
| `Ownership Summary by Wallet(s)` |   na    |
| `NFT Collections`         |       |

SimpleHash has managed to map each concern to one endpoint:  
- What more can I know about this one token? -> NFT by Token ID
- What can I know about this NFT collection? -> NFTs by contract
- What NFTs does this Wallet own? -> NFTs by wallet
- How many transfers did this NFT have? -> Transfers by NFT
- Who are the list of owners of this NFT? -> Owners by NFT / Contract

## As you can see, each endpoint addresses one question, and one question only.

In contrast, Covalent's approach is not so clear cut.

Which are the top performing NFTs and what are their price floors?

What are the token IDs for an NFT collection?

What are the transactions for contract? (Shouldn't it be for particular tokens?)

What are the external metadata of each NFT?


<!-- SimpleHash has abstracted away the need to even know the contextual relevance of 'metadata' in NFT data development. -->



`Global Market View`

## Comparison in response design

## Comparison in performance
