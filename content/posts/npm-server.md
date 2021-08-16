---
title: "Steps for creating an NPM server"
date: 2021-08-16T10:33:24+08:00
# weight: 1
# aliases: ["/first"]
tags: ["node", "notes"]
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
Step 1:
`npm init` in project folder

Step 2:
Add `"start": "node index.js"` in package.json, like so:

```json
{
  "scripts": {
    "start": "node index.js",    "test": "echo \"Error: no test specified\" && exit 1"
  },
}
```
