---
title: "Express: Auto Server Refresh Feature"
date: 2021-08-16T11:29:55+08:00
# weight: 1
# aliases: ["/first"]
tags: ["fullstackopenwk3", "notes", "express"]
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
>One problem with the simple Express server is that you have to restart the application every time there is a change to the source code to see the changes implemented.
>
>By installing nodemon, you have an "auto-refresh upon source code save" feature.

#### Step 0: Go to Express project root.  

#### Step 1: Install nodemon  
`npm install --save-dev nodemon`

#### Step 2: Add the following line: `"dev": "nodemon index.js"` to package.json
```json {hl_lines=[3]}
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js",
  "test": "echo \"Error...""
}
```
