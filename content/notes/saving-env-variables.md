---
title: "Environment Management during staging phase"
date: 2023-02-06T16:00:53+08:00
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

## This article is useful when you're almost ready to push an app live to production. 

Consider the following workflow. You've already implemented most of the frontend and backend of your app, and it's ready to go live in to production. All the while during development, you're using the local Mongodb. You push it live onto Vercel, changing the environment variables for the DB to point to the production DB instead of the test DB. You then deploy it. 

Suddenly, you realize that the DB hasn't been right. The data isn't showing up. You realize that, within your backend routes, you've hard-coded the MongoDB URI as `process.env.MONGODB_URI`, which is itself pointing to the live prod DB. 

What if you need to do tests on the localDB, whilst also connecting to the production db without switching tediously? Well, here's the trick. 

In your `package.json`, add the node-env variable:
```JSON
"scripts": {
    "dev": "NODE_ENV=development next dev",
    "build": "next build",
    "start": "NODE_ENV=production next start",
    "test": "NODE_ENV=test jest --watch",
    "test:ci": "jest --ci",
    "lint": "next lint"
  },
```
This will ensure that when you do `yarn run dev`, the `NODE_ENV` variable will be set to development. 

We can then use this as a flag to run our app in different modes. 

```javascript
const MONGODB_URI = process.env.NODE_ENV === 'test'
    ? process.env.TEST_MONGODB_URI
    : process.env.MONGODB_URI
```
And then, in .env file, we'll have the URI of both databases. 

In my case, I'll need to edit the URI in my mongo connectors. 

That's about it. 