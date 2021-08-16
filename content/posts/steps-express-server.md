---
title: "Steps for creating an Express Server"
date: 2021-08-16T11:07:33+08:00
# weight: 1
# aliases: ["/first"]
tags: ["express", "notes","fullstackopenwk3"]
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
searchHidden: true
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
It is really simple creating an express API server.

All you have to do is basically requiring express, creating/importing the JSON data, setting up a route, setting up a listening port, and *bam* there is API access to your data.

### Steps for creating an Express API server:

Step 1:  Importing Express, assigning it to app variable  
```javascript
const express = require('express')
const app = express()
```

Step 2: Creating JSON data  
```JSON
let persons = [
    {
      "id": 1,
      "name": "Arto Hellas",
      "number": "040-123456"
    },
    {
      "id": 2,
      "name": "Ada Lovelace",
      "number": "39-44-5323523"
    },
    {
      "id": 3,
      "name": "Dan Abramov",
      "number": "12-43-234345"
    },
    {
      "id": 4,
      "name": "Mary Poppendieck",
      "number": "39-23-6423122"
    }
]
```

Step 3: Setting up a route  
```javascript
app.get('/api/persons', (request, response) => {
  response.json(persons)
})
```

Step 4: Setting up a port:
```javascript
const PORT = 3001

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```
