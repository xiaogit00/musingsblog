---
title: "Refactoring Express Backend"
date: 2021-09-10T17:54:07+08:00
# weight: 1
# aliases: ["/first"]
tags: ["fullstackopen", "express", backend]
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

I feel like there is a *lot* to take in for this part. Whilst in the last part, all of the backend logic was basically stored in index.js, now we've refactored each portion into their own separate modules. Indeed, I can see how this could be an overkill for smaller apps, but for more complex projects, it would certainly 'make developing the app much easier'.

Let's take a look at how that is done, line by line.

### Index.js

```javascript
const app = require('./app')
const http = require('http')
const config = require('./utils/config')
const logger = require('./utils/logger')

const server = http.createServer(app)

server.listen(config.PORT, () => {
  logger.info(`Server running on port ${config.PORT}`)
})

```
Index.js is now reduced to simply this. You require the app, http, config, logger, server. Let's take a look at what each of these things do.

#### app.js
```javascript
const config = require('./utils/config')
const express = require('express')
const http = require('http')
const app = express()
const cors = require('cors')
const notesRouter = require('./controllers/notes')
const middleware = require('./utils/middleware')
const logger = require('./utils/logger')
const mongoose = require('mongoose')

logger.info('connecting to', config.MONGODB_URI)

mongoose.connect(config.MONGODB_URI)
  .then(() => {
    logger.info('connected to MongoDB')
  })
  .catch((error) => {
    logger.error('error connecting to MongoDB:', error.message)
  })

app.use(cors())
app.use(express.static('build'))
app.use(express.json())
app.use(middleware.requestLogger)

app.use('/api/notes', notesRouter)

app.use(middleware.unknownEndpoint)
app.use(middleware.errorHandler)

module.exports = app

```
App.js is basically the entire backend app. Its main responsibilities include:
- connecting to database
- using the static build `app.use(express.static('build'))`
- using the request logger  `app.use(middleware.requestLogger)`
- loading the routes we'll use `app.use('/api/notes', notesRouter)`
- handling unknown endpoints and errors with middleware `app.use(middleware.unknownEndpoint)`

So far, the responsibilities are rather clear from the top level. However, we see right at the top that it requires a couple of modules. I'll go into detail into the following:
- config
- http
- notesRouter
- middleware
- logger

##### config.js
```javascript
require('dotenv').config()

const PORT = process.env.PORT || 3001

const MONGODB_URI = process.env.MONGODB_URI

module.exports = {
  MONGODB_URI,
  PORT
}
```
The job of config.js is rather simple. It is responsible for making the following config variables available for external consumption via the syntax "config.MONGODB_URI" and "config.PORT". How it does that, is by using the dotenv module, which looks for the .env file, and sets those two variables using the process.env.PORT method. This way, all your secret configurations can be stored in the config file, and instead of accessing it directly using process.env, I have a better abstraction.

The config.js module is used in app.js, to connect to URI, and also in index.js, to listen on the port number.

##### http
```javascript
const http = require('http')
const server = http.createServer(app)
server.listen(config.PORT, () => {
  logger.info(`Server running on port ${config.PORT}`)
})
```
So looking at [this](https://www.youtube.com/watch?v=VLXAzzRjQws) youtube video of node http module, it seems like http allows you to create web servers...which begs the question: how have I been doing it?! Cos within index.js, the refactored version uses `const server = http.createServer(app)` whereas previously, the server I created was an express server, which I created using const app = express(). What's the difference between creating an express server and a http server?

Looking [online](https://stackoverflow.com/questions/17696801/express-js-app-listen-vs-server-listen), it seems like the answer is that creating a HTTP server yourself is useful if you want to reuse the HTTP server, for example if you want to do something on the same server instance.

But that answer doesn't really match my question. It seems from the code, that the express app is what you *pass* into the http server using the createServer method of the http object. I think for now, it's not that important for me to understand what's going on under the hood, but to know how servers are created. The key idea here, is that servers are simply instances situated on the machine, that listens to requests on a particular port, and gives a response back to the client. The http.createServer method creates a server *in the nature of the express app*.

##### notesRouter
```javascript
///controller/notes.js
const notesRouter = require('express').Router()

const Note = require('../models/note')

notesRouter.get('/', (request, response) => {
  Note.find({}).then(notes => {
    response.json(notes)
  })
})
//...and other routes

module.exports = notesRouter

```
The main responsibility of this module is to define the routes in our application, and to serve the right data to that route. It is parked under `controllers` section because it is essentially a controller: under the MVC framework, the job of a controller is to receive a route, define the kinds of actions a user can do to a particular route, and interact with the underlying data model.

The reason why we've created a separate notesRouter is because these are a series of routes that interact with the notes model. It's common practice within bigger applications for there to be one controller for each model.


What's important to note here is the first line. Here, we required a Router *object*. According to docs, a router object is *an isolated instance of middleware and routes*. It's a 'mini application', capable only of performing middleware and routing functions. Just like how you define routes to an express app with app.get(route, request handler), now you can define a separate series of routes using notesRouter.get()/ notesRouter.put().

Then, at the end, we use this syntax for exporting: module.exports = notesRouter, so that other applications can access it.

##### middleware
Middleware refactors the middleware functions of my app. First, there is a requestLogger, which helps log information about requests on my console. A majorly useful tool for development. Next, there are the error handlers, which are all done through middleware. Within app.js, the middleware is invoked as such:

```javascript
app.use(middleware.requestLogger)
```

Within the context of the app, the middleware is used at the places they need to be used. For instance, requestLogger is used at the start, unknownEndpoint used at the bottom along with errorHandler.

I need a further post dedicated to how exactly middleware works, because it seems to be an extremely important topic within backend development.

##### logger
Finally, the logger module. The logger module is pretty simple from the get go: it contains two functions, one is info, and another one is error. These functions take in *a list of variables* using the object spread syntax, `...params`.

```javascript
//logger.js
const info = (...params) => {
  console.log(...params)
}

const error = (...params) => {
  console.error(...params)
}

module.exports = {
  info, error
}
```
So, if you pass the following `logger.info('Method:', request.method)`, like in middleware, it would take those arguments, 'method' and 'request.method', and pass it onto console.log. `...params` simply says, take however many params there are, and just pass them on as a list of the same.

Similarly, you pass the same to console.error.

That's about it for all the different modules.
