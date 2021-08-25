---
title: "Steps for Setting Up the Backend During Development"
date: 2021-08-25T12:41:55+08:00
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
Alrighttttt I am about to embark on some backend development for my Streams app. This document acts as a kind of guide for future development processes, as the exact processes are rather rusty in my mind.

I am starting out from *completely no backend* on my simple React app, with a couple of frontend components.

I think the first thing I need to do is to set up an express server.

My knowledge is rusty lol. Express is simply a library that offers a pleasing interface for backend development: i.e. setting up routes where you can offer raw data in the JSON format to the frontend.

The first thing to know, is that you will need to create **another** folder altogether to work on the backend, because the **backend is a standalone application**.

### Steps for creating the backend folder

#### Step 1. Create a new backend folder.
In my case, I will create a folder called 'streamsapp_backend'

#### Step 2: cd to that folder.

#### Step 3: run `npm init`
Answer the questions as follows:
```JSON
{
  "name": "backend",
  "version": "0.0.1",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Matti Luukkainen",
  "license": "MIT"
}
```
This will create a package.json file.

#### Step 4: Add `"start": "node index.js"` in the package.json file, like so:
```json
"scripts": {
  "start": "node index.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

#### Step 5: Do a quick test to see if everything's working:
- add `console.log('hello world')` in index.js.
- run `node index.js` from command line
- or, run `npm start`

#### Step 6: Install express into the backend
`npm install express`

### Now, you can beginning coding your server

#### Step 7: Within index.js, import express and create an app.


```javascript
const express = require('express')
const app = express()
```

#### Step 7: Place some placeholder JSON data within a variable to begin working setting up routes
```javascript
let notes = [
  ...
]
```
#### Step 8: Create the routes below:

```javascript
app.get('/', (request, response) => {
  response.send('<h1>Hello World!</h1>')
})

app.get('/api/notes', (request, response) => {
  response.json(notes)
})

const PORT = 3001
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```
The entire index.js file looks something like this:
```javascript
const express = require('express')
const app = express()

let notes = [
  ...
]

app.get('/', (request, response) => {
  response.send('<h1>Hello World!</h1>')
})

app.get('/api/notes', (request, response) => {
  response.json(notes)
})

const PORT = 3001
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```
> ### Important to note that you're using another port for backend.

#### Step 9: Test the route to see if requests are going through.
1. `npm start`

2. Go to localhost:3001/notes and see if the server delivers the JSON response.

### Further Optimizations:

#### Step 10: Install nodemon
So that you won't need to restart server whenever there's changes to backend source code.
`npm install --save-dev nodemon`

Then, add the following to *package.json*:

`"dev": "nodemon index.js",`
under scripts.start.

### To start server after this point, run `npm run dev`

### Some references:
#### Getting single data
```javascript
app.get('/api/notes/:id', (request, response) => {
  const id = Number(request.params.id)
  const note = notes.find(note => note.id === id)

  if (note) {    
    response.json(note)  
  } else {    
    response.status(404).end()  
  }})
```

#### Deleting resource
```javascript
app.delete('/api/notes/:id', (request, response) => {
  const id = Number(request.params.id)
  notes = notes.filter(note => note.id !== id)

  response.status(204).end()
})
```
### Using Postman

#### Step 1 - Opening postman from laptop top bar.
Begin Sending requests.


That's about it! You've set up your express backend server. Great. 
