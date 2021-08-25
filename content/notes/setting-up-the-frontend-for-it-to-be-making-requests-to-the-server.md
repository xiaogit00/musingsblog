---
title: "Setting Up the Frontend for It to Be Making Requests to the Server"
date: 2021-08-25T16:07:43+08:00
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
Assuming that you've not set up anything.

#### Step 1: Installing Axios
`npm install axios`

#### Step 2: Importing and sending request
```javascript
import axios from 'axios'
axios
  .get('http://localhost:3001/notes')
  .then(response => {
    const notes = response.data
    console.log(notes)
  })
```

For most part, that will suffice. However, you might want to call the data via an effect hook.

#### Step 3: Calling request with an effect hook:
```javascript
import React, { useState, useEffect } from 'react'

const [streams, setStreams] = useState([])

useEffect(() => {
  axios
    .get('http://localhost:3001/notes')
    .then(response => {
      setStreams(response.data)
    })
}, [])
```
This will set the streams variable up. 
