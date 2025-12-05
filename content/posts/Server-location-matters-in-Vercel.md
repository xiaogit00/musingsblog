---
title: "Server Location Matters in Vercel"
date: 2023-09-01T11:50:16+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Deployment", "Production", "NextJs"]
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
My NextJs application had a problem: whenever it did CRUD operations, the server takes around 8 seconds to respond on first attempt, and then 2/3 seconds on subsequent attempts. 

This is the so called cold start problem that Vercel is known for. The source of the latency does not come from the DB as I've tested it in development mode against the same DB and there was no latency there. 

After banging about the interwebs trying to see if the right way to do it is to implement SWR or firing up a cron job to shoot requests to the pages every few minutes, I stumbled upon this write up on [Setting Serverless Function Regions](https://vercel.com/docs/functions/serverless-functions/regions). 

In it, it is explained that
> Serverless Functions enable you to run code on-demand and at scale without setting up your own compute infrastructure. When people visit one of your API routes that uses a Serverless Function, the Function is invoked in a region on Vercel's Edge Network. **If your Function depends on a database, and is invoked in a region far from that database, the response time will be slow.**

My project's function region on Vercel has been set to USA East by default. **My MongoDb instance, however, is configured for Singapore!**

After switching it over to the Singapore region, I witnessed a miracle. The latency dropped substantially, to a barely noticeable amount. 

All it took was a switch...

So my question now is, why did this occur? 

My intuition is telling me that first, I am firing off a request to a US server. That, in itself, carries latency. Then, the server is requesting for data from the Singapore database. The SG database then responds with the data to the US server, which then carries to my app here. The data has by now travelled across the globe 3/4 times.  

Proud of this debugging session. Now the production app feels as zippy as my localhost version. 🥰