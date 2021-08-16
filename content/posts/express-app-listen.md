---
title: "Express App.listen"
date: 2021-08-16T10:55:57+08:00
# weight: 1
# aliases: ["/first"]
tags: ["fullstackopenwk3"]
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
What I've found is that if I don't add the following code to my `index.js` file, I am not able to run the localhost:
```javascript
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```

The code still runs when you do `npm start`, but upon end of code, it simply terminates, whereas if you do `app.listen`, you could inspect the output at a port. 
