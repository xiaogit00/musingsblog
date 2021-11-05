---
title: "Overview of Dexs On-Chain Data Using Dune Analytics 2021 (Part 1)"
date: 2021-11-03T12:09:45+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Dex", "Uniswap", "Data Exploration", "SQL", "onChain data"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
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
Dune analytics allows you to query on chain data using SQL queries via its web app. Although execution time is a little slow due to the large data volume (~14s for a simple query, ~1min+ for more complex ones), it is still one of the most useful products for basic on chain analysis I've used so far. The prerequisite, though, is that you would need to know a bit of SQL.

In this post, I'll be taking you through a simple exploration of Dex on-chain data to uncover some macro level insights. Specifically, I will answer the following questions:  
- What kinds of insights can we derive from the data on Dexs?  
- What kind of Macro level information can we get about Dex trading activities?
- When is the first trade of each Dex?  
- What's the total number of transactions on each Dex to date?  
- What's the % breakdown of the number of transaction per year across Dexs?  
- What's the average value of each transaction across Dexs?  

In the process, I'll document my SQL queries as well in case you want to try it for yourself. Let's get started!  


### Using Dune Analytics
Dune Analytics has a tonne of tables that you can query. In this article, I'll be using the `dex.trades` [table](https://docs.dune.xyz/data-tables/data-tables). The data on dex.trades shows all transaction recorded on Dexs. You could search by contract address for the particular pool. You could rank value of the pools of similar asset type by protocol. The kinds of queries you can do are endless. Below is the table schema (field name in bold), followed by short descriptions of the field:

***dex.trades table schema:***
- [date] ***block_time***
- [string] ***token_a_symbol*** - ‘swap from’
- [string] ***token_b_symbol*** - ‘swap to’
- [int] ***token_a_amount***
- [int] ***token_b_amount***
- [string] ***project*** - protocol (e.g. Uniswap)
- [int] ***version*** - 2 or 3
- [string] ***category*** - e.g. DEX / Aggregator
- [address] ***trader_a*** - same as tx_from
- [address] ***trader_b*** - none if it’s to contract
- [largeInt] ***token_a_amount_raw*** - a very large number
- [largeInt] ***token_b_amount_raw*** - a very large number
- [int] ***usd_amount*** - swap value nominal
- [address] ***token_a_address*** - token address
- [address] ***token_b_address*** - token address
- [address] ***exchange_contract_address*** - contract address
- [tx_hash] ***tx_hash***
- [address] ***tx_from*** - sent from
- [address] ***tx_to*** - sent to
- *trace_address*


## Basic Data Exploration: 29 Dexs, 3551 Unique tokens

Let's begin with basics. Querying the table, we find that there are a total of 29 Dex protocols, out of which 22 are Dexs and 7 are Aggregators. They provide 3551 unique tokens for trading. Below are the supporting queries.

>Query 1: What's the total number of tokens traded across all Dexs?
```SQL
SELECT DISTINCT count(token_a_symbols)
FROM dex.trades
```

>Results:  
`3531`

There are 3531 unique tokens across all Dexs.

>Query 2: How many different projects are there in the dex.trades table? What are their categories?

```SQL
SELECT DISTINCT project, category
FROM dex.trades
```
>Results:
| **Project**                | **Category** |
| -------------------------- | ------------ |
| Gnosis Protocol            | DEX          |
| Paraswap                   | Aggregator   |
| 0x API                     | Aggregator   |
| 1inch Limit Order Protocol | DEX          |
| 1inch                      | Aggregator   |
| Oasis                      | DEX          |
| Tokenlon                   | Aggregator   |
| Futureswap                 | DEX          |
| mistX                      | Aggregator   |
| 0x Native                  | DEX          |
| Balancer                   | DEX          |
| LINKSWAP                   | DEX          |
| Gnosis Protocol            | Aggregator   |
| Sushiswap                  | DEX          |
| Clipper                    | DEX          |
| DODO                       | DEX          |
| Matcha                     | Aggregator   |
| airswap                    | DEX          |
| 1inch LP                   | DEX          |
| Mooniswap                  | DEX          |
| DDEX                       | DEX          |
| Kyber                      | DEX          |
| IDEX                       | DEX          |
| Synthetix                  | DEX          |
| Curve                      | DEX          |
| dYdX                       | DEX          |
| Bancor Network             | DEX          |
| Uniswap                    | DEX          |
| Loopring                   | DEX          |

There are 28 projects in the dex.trades table. 22 are Dexs and 7 are Aggregators.


# Getting a sense of historicity

### IDEX was the first active DEX.

The earliest Dex transaction was conducted on 9 Sep 2017, on IDEX protocol. This is found by making the following query:

>Query: When is the earliest transaction on Dex conducted?
``` SQL
SELECT *
FROM dex.trades
ORDER BY block_time
LIMIT 1
```

This is the tx hash: `0x87bbc5fe23574dc757ab818a9011cf6c18bf5066f57788e6dd2bd1fc147eae25`  

### Other Dexs only came in the picture in 2019. The first three Dexs were Uniswap, Kyber, and Oasis, all starting in early 2019.

These are the dates where each protocol became active:

>Query 3: When was the first trade of each Dex?
```SQL
SELECT project, min(block_time)
FROM dex.trades
GROUP BY project
ORDER BY 2
```

>Results:
```
IDEX        2017-09-27 22:57
Uniswap     2019-01-01 00:02
Kyber       2019-01-06 07:59
Oasis       2019-02-08 13:47
dYdX        2019-05-15 16:53
1inch       2019-06-03 20:15
0x Native   2019-07-13 06:46
Tokenlon    2019-07-14 07:33
DDEX        2019-08-26 09:09
Synthetix   2019-09-26 10:15
Paraswap    2019-11-12 16:18
airswap     2019-12-20 20:24
Curve       2020-01-03 18:57
Bancor Network 2020-01-09 16:06
Loopring       2020-01-20 11:40
Gnosis Protocol 2020-01-27 16:35
0x API      2020-01-29 15:24
Balancer    2020-03-13 20:55
Matcha      2020-03-24 21:14
DODO        2020-08-10 13:19
Mooniswap   2020-08-10 23:44
Sushiswap   2020-09-04 10:44
LINKSWAP    2020-12-02 00:40
Futureswap  2021-01-04 23:23
1inch LP    2021-01-08 00:01
mistX       2021-05-25 17:04
1inch Limit Order Protocol  2021-06-08 22:39
Clipper     2021-06-26 01:59
```
---
# Usage activity

### The top three most active Dexs are of all time are Uniswap, Sushiswap, IDEX, 1Inch, and Balancer.

Uniswap wins by a wide margin, boasting 72.2 million transactions on its protocol til date. Sushiswap follows behind with 5.9 million transactions.

>Query: What is the total number of transactions for each network, all time?
```SQL
SELECT project, count(*) transactions
FROM dex.trades
GROUP BY 1
ORDER BY 2 DESC
```

>***Results:***
| **Project**     | **No\_of\_Transactions** |
| --------------- | ------------------------ |
| Uniswap         | 72227929                 |
| Sushiswap       | 5971121                  |
| IDEX            | 5911015                  |
| 1inch           | 3914366                  |
| Balancer        | 2644736                  |
| 0x API          | 2592171                  |
| 0x Native       | 2266774                  |
| Loopring        | 2130333                  |
| Kyber           | 1503932                  |
| Bancor Network  | 1336811                  |
| dYdX            | 840452                   |
| Tokenlon        | 824131                   |
| Matcha          | 627771                   |
| Curve           | 452986                   |
| Paraswap        | 407842                   |
| Oasis           | 342767                   |
| DODO            | 290684                   |
| Mooniswap       | 271686                   |
| Gnosis Protocol | 131055                   |
| airswap         | 97627                    |
| Synthetix       | 90154                    |
| LINKSWAP        | 64905                    |
| mistX           | 62228                    |
| DDEX            | 46681                    |
| Clipper         | 45349                    |

---

### Uniswap made 65% of its total transactions in 2021, compared to 87% for Sushiswap.

Both Uniswap and Sushiswap have grown tremendously between last year and this year. A large portion of Sushiswap's transactions (87%) were made this year, compared to a smaller percentage for Uniswap (65%). 1Inch is also way more utilized this year at 86%. Balancer, on the other hand, saw greater usage in 2020 than 2021.

>Query: What is the percentage of total tx that occurred in 2019, 2020, 2021, across networks?
```SQL
WITH t1 as (
    SELECT project, COUNT(*) tx_2019
    FROM dex.trades
    WHERE block_time BETWEEN '2019-01-01' AND '2020-01-01'
    GROUP BY 1
),
    t2 as (
    SELECT project, COUNT(*) tx_2020
    FROM dex.trades
    WHERE block_time BETWEEN '2020-01-01' AND '2021-01-01'
    GROUP BY 1
),
    t3 as (
    SELECT project, COUNT(*) tx_2021
    FROM dex.trades
    WHERE block_time > '2021-01-01'
    GROUP BY 1
),
    t4 as (
    SELECT project, count(*) total
    FROM dex.trades
    GROUP BY 1
    )

SELECT t4.project,
    t1.tx_2019,
    t2.tx_2020,
    t3.tx_2021,
    t4.total
FROM t4
LEFT JOIN t2
ON t4.project = t2.project
LEFT JOIN t3
ON t4.project = t3.project
LEFT JOIN t1
ON t4.project = t1.project
ORDER BY 5 DESC
```
>***Result:***  *note: I converted the raw data to % in excel
| **Project**                | **2019** | **2020** | **2021** | **Total tx** |
| -------------------------- | -------- | -------- | -------- | ------------ |
| Uniswap                    | 0.8%     | 34.1%    | 65.1%    | 72238557     |
| Sushiswap                  | 0.0%     | 12.7%    | 87.3%    | 5972200      |
| IDEX                       | 31.4%    | 17.0%    | 0.0%     | 5911015      |
| 1inch                      | 0.3%     | 12.9%    | 86.9%    | 3914815      |
| Balancer                   | 0.0%     | 54.8%    | 45.2%    | 2644847      |
| 0x API                     | 0.0%     | 7.5%     | 92.5%    | 2592171      |
| 0x Native                  | 16.2%    | 46.2%    | 37.7%    | 2266895      |
| Loopring                   | 0.0%     | 95.2%    | 4.8%     | 2130333      |
| Kyber                      | 33.0%    | 62.3%    | 4.6%     | 1503932      |
| Bancor Network             | 0.0%     | 39.8%    | 60.2%    | 1336949      |
| dYdX                       | 11.1%    | 72.8%    | 16.1%    | 840452       |
| Tokenlon                   | 9.0%     | 54.0%    | 37.1%    | 824155       |
| Matcha                     | 0.0%     | 14.7%    | 85.3%    | 627771       |
| Curve                      | 0.0%     | 49.5%    | 50.5%    | 452999       |
| Paraswap                   | 0.1%     | 12.5%    | 87.4%    | 407922       |
| Oasis                      | 61.8%    | 38.1%    | 0.1%     | 342767       |
| DODO                       | 0.0%     | 26.1%    | 73.9%    | 290691       |
| Mooniswap                  | 0.0%     | 86.0%    | 14.0%    | 271687       |
| Gnosis Protocol            | 0.0%     | 36.2%    | 63.8%    | 131085       |
| airswap                    | 0.2%     | 7.6%     | 92.3%    | 97643        |
| Synthetix                  | 15.7%    | 55.1%    | 29.2%    | 90154        |
| LINKSWAP                   | 0.0%     | 15.3%    | 84.7%    | 64905        |
| mistX                      | 0.0%     | 0.0%     | 100.0%   | 62244        |
| DDEX                       | 14.1%    | 83.6%    | 2.3%     | 46681        |
| Clipper                    | 0.0%     | 0.0%     | 100.0%   | 45354        |
| 1inch Limit Order Protocol | 0.0%     | 0.0%     | 100.0%   | 44009        |
| Futureswap                 | 0.0%     | 0.0%     | 100.0%   | 11860        |
| 1inch LP                   | 0.0%     | 0.0%     | 100.0%   | 9367         |


---

### Uniswap is 9 times more active than Sushiswap in 2021.

The top 5 most active Dexs this year are Uniswap, Sushiswap, 1Inch, 0x API, Balancer. Uniswap is 9 times more active than runner up Sushiswap.

>Query: Total number of transactions in 2021 across networks?
```SQL
SELECT project, COUNT(*) tx_2021
FROM dex.trades
WHERE block_time > '2021-01-01'
GROUP BY 1
ORDER BY 2 DESC
```  


>***Results:***
| **Project**                | **Transaction 2021** |
| -------------------------- | -------------------- |
| Uniswap                    | 46998361             |
| Sushiswap                  | 5214656              |
| 1inch                      | 3400754              |
| 0x API                     | 2398562              |
| Balancer                   | 1195776              |
| 0x Native                  | 853554               |
| Bancor Network             | 804868               |
| Matcha                     | 535784               |
| Paraswap                   | 356337               |
| Tokenlon                   | 305534               |
| Curve                      | 228912               |
| DODO                       | 214833               |
| dYdX                       | 135404               |
| Loopring                   | 102336               |
| airswap                    | 90098                |
| Gnosis Protocol            | 83583                |
| Kyber                      | 69490                |
| mistX                      | 62248                |
| LINKSWAP                   | 54974                |
| Clipper                    | 45354                |
| 1inch Limit Order Protocol | 44009                |
| Mooniswap                  | 38111                |
| Synthetix                  | 26330                |
| Futureswap                 | 11860                |
| 1inch LP                   | 9367                 |
| DDEX                       | 1068                 |
| Oasis                      | 345                  |




# Transaction size
What is the average transaction size on each protocol, and what does it tell us about its users? Let's find out.

### The average transaction size on Curve is $175k USD, 22 times higher than on Uniswap.

>Query: What is the average value of each transaction across networks (all time)?
```SQL
SELECT project, ROUND(AVG(usd_amount))
FROM dex.trades
GROUP BY 1
ORDER BY 2 DESC
```

>Results:
| **Project**                | **Avg\_Value\_per\_Tx** |
| -------------------------- | ----------------------- |
| Curve                      | 175111                  |
| Synthetix                  | 124752                  |
| 1inch LP                   | 77584                   |
| DODO                       | 77004                   |
| 1inch Limit Order Protocol | 44668                   |
| Matcha                     | 36195                   |
| Paraswap                   | 30875                   |
| Tokenlon                   | 25234                   |
| 1inch                      | 24710                   |
| 0x Native                  | 23302                   |
| Sushiswap                  | 22799                   |
| Bancor Network             | 17453                   |
| 0x API                     | 15758                   |
| Gnosis Protocol            | 14536                   |
| Balancer                   | 9992                    |
| dYdX                       | 9828                    |
| airswap                    | 9415                    |
| mistX                      | 9019                    |
| Uniswap                    | 7812                    |
| Clipper                    | 4682                    |
| Kyber                      | 4042                    |
| LINKSWAP                   | 4008                    |
| Oasis                      | 3224                    |
| DDEX                       | 3087                    |
| Mooniswap                  | 2999                    |
| IDEX                       | 1204                    |
| Loopring                   | 122                     |

***Analysis:***
The results for this query is very interesting. It tells us that although Uniswap is the most active Dex, trades happening there are actually not of very high value. On the contrary, protocols like Curve and Synthetix see an astonishing average value per transaction at more than 100kUSD, with Curve leading the way at 175k USD / tx. That is 22x larger than the average tx value on Uniswap. That is an incredible amount of money for every transaction. What does it tell us about the demographic of users using Curve? It is possible that Curve has a greater user base of whale / whale-ish individuals. At the very least, there is something interesting for us to investigate further.

### Synthetix sees highest average value per transaction at $327708 USD in 2021.

Looking at the data for 2021, we see that the average transaction value on Synthetix has risen sharply to $327k, topping Curve. The average transaction value on Curve this year is also higher than its overall average, by around 75k. More people are trading large volumes per transaction as they become more comfortable with Defi. The average transaction value has also increased for Uniswap.  Interestingly, for this year, the average transaction size for SushiSwap is almost twice that of Uniswap.  

>*Query: What is the average value of each transaction across networks (this year, 2021)?*
```SQL
SELECT project, ROUND(AVG(usd_amount))
FROM dex.trades
WHERE block_time > ’2021-01-01’
GROUP BY 1
ORDER BY 2 DESC
```

>Results:
| **Project**                | **Avg\_Value\_per\_Tx** |
| -------------------------- | ----------------------- |
| Synthetix                  | 327708                  |
| Curve                      | 256062                  |
| DODO                       | 106418                  |
| 1inch LP                   | 77584                   |
| 0x Native                  | 53045                   |
| Tokenlon                   | 52152                   |
| 1inch Limit Order Protocol | 44668                   |
| dYdX                       | 42736                   |
| Kyber                      | 40786                   |
| Matcha                     | 39828                   |
| Paraswap                   | 35235                   |
| Bancor Network             | 27881                   |
| 1inch                      | 25920                   |
| Sushiswap                  | 24551                   |
| Oasis                      | 23221                   |
| Gnosis Protocol            | 21673                   |
| Balancer                   | 18194                   |
| 0x API                     | 16325                   |
| Uniswap                    | 10887                   |
| airswap                    | 9579                    |
| mistX                      | 9019                    |
| Mooniswap                  | 5687                    |
| DDEX                       | 5489                    |
| Clipper                    | 4682                    |
| LINKSWAP                   | 4267                    |
| Loopring                   | 1177                    |


---

### Conclusion
There are 28 active dexs/aggregators that trade a total of 3531 tokens. The first Dex transaction was conducted on 9th Sep 2017, on IDEX. The other Dexs, including Uniswap, only came into the picture in 2019. Uniswap has the highest number of transactions till date - 72,227,929, followed by SushiSwap with 5,971,121.

65% of all transactions on Uniswap was done in 2021 (this year), whereas 87% of Sushiswap's transactions are done this year. The average value of each transaction on Uniswap is only $7812USD - this pales in comparison to the top (Curve), of $175111 USD - showing us that there are many protocols where the average value of transaction is way higher than Uniswap. The most likely cause of this is due to slippage. Platforms like Uniswap incentivizes smaller transactions by design. Further drill down needs to be done to uncover differences in user demographic and behavior in each Dex.

I hope you've enjoyed this data exploration. Stay tuned for the next part as we dig deeper into the data. 
