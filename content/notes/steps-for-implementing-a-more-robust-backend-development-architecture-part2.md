---
title: "Steps for Implementing a More Robust Backend Development Architecture Part2"
date: 2021-09-23T10:38:23+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Backend", "Express", "MongoDB","Documentation"]
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
In part 1, we mainly looked at laying out the groundwork for the backend project, including setting up console logging (morgan), eslint, installing various dependencies, configuration for MongoDB connection, util files such as logger, creating the models I needed, *coding the express app (app.js)*, and finally, populating the database with some initial data to work with.

In this part, I'll begin by documenting how I go about creating and testing the routes. Then, we'll look at middleware and error handling. Finally, I'll connect the frontend to the backend to make sure that all the data is being pulled correctly.

## Setting up Routers
The first thing I needed to do is to create some routes so that I could display the data on my browser. These are the steps for creating the routers.

### Step 1: Within App.js, code how you'd like to call the routes.
```javascript
const streamsRouter = require('./controllers/streams')
const tradesRouter = require('./controllers/trades')
//...
app.use('/api/streams', streamsRouter)
app.use('/api/trades', tradesRouter)
```
Here, you basically use the various routes for these end points.

### Step 2: Code the Routers:
1. Create a controllers folder.
2. Create *streams.js* and *trades.js*
3. Code the router.

StreamsRouter:
```javascript
const streamsRouter = require('express').Router() //make a new router instance
require('express-async-errors') //for better error handling
const Stream = require('../models/stream')
const Trade = require('../models/trade') //this is important for populate


streamsRouter.get('/', async (request, response) => {
    const streams = await Stream.find({}).populate('trades')
    response.json(streams)
})

streamsRouter.get('/:id', async (request, response) => {
    const stream = await Stream.findById(request.params.id).populate('trades')
    response.json(stream)
})

streamsRouter.post('/', async (request, response) => {
    const body = request.body
    // console.log(body)
    if (!body.asset) {
        return response.status(400).end()
    }

    const stream = new Stream({
        asset: body.asset,
        assetClass: body.assetClass,
        trades: body.trades, //array
        swaps: body.swaps
    })

    const savedStream = await stream.save()
    response.json(savedStream)
})

streamsRouter.delete('/:id', async (request, response) => {
    await Stream.findByIdAndRemove(request.params.id)
    response.status(204).end()
})



module.exports = streamsRouter

```
It's pretty straight forward: for each route, invoke the Model, and return the required response data as JSON.

Perhaps some things to remember on Model interactions:

The get route uses ***Stream.find({})***. The get/:id route uses ***Stream.findById(request.params.id)***. Both uses .populate('trades') to swap the object IDs in the trades field with the actual objects from Trades. An important note here is that if you don't require(trades) model at first, this would not work.

The post route uses request.body to create a ***new Stream()*** object, setting the properties of the object to be the various properties of the body. Then you save stream (***stream.save()***) to propagate it to the database, and return a response.

Finally, the delete route uses ***Stream.findByIdAndRemove***. In some ways, this is very intuitive and completely similar to Laravel.

After coding each route, you test it with Postman to see that it returns the right response.

## Laying the groundwork for testing work
The next thing I needed to do was to have a way to populate the database with *workable* sample data on every test. My current infrastructure did not support instant population and deletion (essential for testing work), because the IDs were generated manually.

To resolve this, I needed to refactor the process of populating records for automated testing work.

### Step 3: Refactor Initialize DB process
1. Save and commit - make a new branch because things might fuck up and you don't want to ruin the current state.
2. Go to the API data end points (with already generated ID fields), copy the JSON file, convert it to Javascript object online, and save it into sampleData.js
3. Create a testDB connection to keep state of previous DB in tact
    - change .env file
        - create a new MongoDB_URI_TEST string (change the part that specifies a new database)
    - change config.js file. That's it.
4. Edit the models to add an _id field for streams - this overwrites the default creation of _id field, and edit the sampleData.js to match.
5. Initialized - drop collections, create new ones all at once (using method documented in part 1)

In the next steps, you'll be writing automated functional tests to check for working state of routes.

## Testing Routes
