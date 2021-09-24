---
title: "Notes on Error handling"
date: 2021-09-24T09:37:40+08:00
# weight: 1
# aliases: ["/first"]
tags: ["error handling", "express", "backend"]
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

The hallmark of an experienced developer is his/her ability to foresee the errors and handle them appropriately. The below are some notes regarding how error handling is done in different contexts.

Let's first take a look at the general *structure* of error handling.
##Structure of error handling

### Within a .then().catch() block
For instance, within a route, you want to find the noteById, and then do something.
```javascript
Note.findById(id)
    .then(note => note)
    .catch(error => {
        console.log(error)
        response.status(500).end()
    })
```
Often times, when you're making a request to the database, it's unclear whether your request will succeed. If there is an error, you'll need a `.catch()` block to handle the error. The catch block receives a callback function with error as argument - an error handler function - and you'll then define how to handle this error.

### Within an async/await block - try-catch
Within POST requests, it's common for us to be communicating with the database this way:
```javascript
const savedNote = await note.save()
response.json(savedNote)
```
However, the operation `note.save()` might encounter errors. It's important for us to try to catch the errors, if not, it'll return UnhandledPromiseRejectionWarning

The typical way to handle errors for async is within a try catch:

```javascript {hl_lines=["4-5"]}
try {
    const savedNote = await note.save()
    response.json(savedNote)
} catch(exception) {
    next(exception)
}
```
Note what you're tryna do with the exception. First, you catch exception. Exception is an object. The try catch logic reads: try the code - if it fails, it'll return an object. Name the error object exception and pass it into your catch statement. Within it, `next()` the exception - which passes it to error handling middleware.

##Types of Errors

Now, it is worthwhile to consider the problem from the perspective of the **different type** of errors you might encounter when dealing with HTTP requests.

### Not found Errors (promise actually resolved)
Take, for instance, the a GET request to a particular ID. If the ID does not exist, (i.e. the object doesnt' exist in the database), we want to set response status to 404 instead of the default for response which is null. Within the typical .then() statement then, we implement:

```javascript {hl_lines=["5-6"]}
Note.findById(request.params.id)
    .then(note => {
      if (note) {        
          response.json(note)      
      } else {        
          response.status(404).end()      
      }
```

Here, notice there is *no exception* thrown. The response simply is null - we're setting ourselves the response status to be 404.

### Promise rejected
However, it could be possible that there is an error, when ***the promise returned by the findById method is rejected***. What are the cases for which the promises are rejected? I seem to have seen quite a lot in my development...Will need to take note. For now, when promise is rejected, the exception is handled in a catch block. This is the foundational principle of promises. Within the catch block, you set what you want to do to the error message:
```javascript
    .catch(error => {      
        console.log(error)      response.status(500).end()    
    })
```

### Malformed ID - 400 bad request
If you give a malformed ID, the findById method will will throw an error causing promise to be rejected. Catch block will be called. In cases of malformed ID, you'll want to display (400), and hopefully send the response of error: 'malformed id'

Generally, when dealing with promises, it's always a good idea to add error and exception handling, because otherwise you find yourself dealing with strange bugs.

Also, it's never a bad idea to print the obejct tha caused the exception to the console in the error handler. The reason error handler gets called might be something completely different than what you had anticipated.


##Moving Error handling to middleware
The main principle is this. Instead of handling the errors within the individual routes themselves, by specifying what to do with the errors in the catch() blocks, we can simply specify an error handler that *delegates* or *passes on* the handling to the errorHandler middleware that'll ultimately 'catch' all these errors. The way we do that is:  
`.catch(error => next(error))`  

A key behavior of next() to note is this. If next is called without a parameter, like so `next()`, the execution will move to the next route or middleware. If next() is called with a parameter, then execution will continue to the error handler middleware.

### Express error handler middlewares:

```javascript
const errorHandler = (error, request, response, next) => {
    logger.error(error.message)

    if (error.name === 'CastError') {
        return response.status(400).send({ error: 'malformatted id' })
    } else if (error.name === 'ValidationError') {
        return response.status(400).json({ error: error.message })
    }

    next(error)
}
```

Errorhandler middlewares accept 4 params. Importantly, note the error object passed to handler: it contains both a message and name property. You can print the message, and identify the error type by `error.name`. ***Metaphysically, whenever an error occurs and you next() it, you're directed automatically to the errorHandler middleware, where you will specify what to do for the different errors by identifying it by its name***. Most of the time, you want to response with a status code of 404 or 400, and either *.send()* or *.json* the message so it displays. `.send` will set content type of text/html which means the response you send is text. `.json` will send a json response.

## Within Async/await
Again, most of the time, you will need to handle exceptions in await requests by putting it into a catch block:
```Javascript

notesRouter.delete('/:id', async (request, response, next) => {
  try {
    await Note.findByIdAndRemove(request.params.id)
    response.status(204).end()
  } catch (exception) {
    next(exception)
  }
})
```
However, if you install `npm install express-async-errors` and `require('express-async-errors')` in your routes.js, you can simply do:

```Javascript
notesRouter.delete('/:id', async (request, response) => {
  await Note.findByIdAndRemove(request.params.id)
  response.status(204).end()
})
```

If there is an exception for the async route, next(exception) is automatically called and execution is passed to error handling middleware.
