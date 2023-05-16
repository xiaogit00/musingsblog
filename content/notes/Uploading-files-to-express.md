---
title: "Uploading Files to Express"
date: 2023-05-16T12:19:37+08:00
# weight: 1
# aliases: ["/first"]
tags: ["upload", "express", "backend"]
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

I've never uploaded a blob file to an express backend, or MongoDB before. 

All the times that I've learned backend development, I was mainly handling JSON data types. 

Which is why, when I tried sending a POST request to an express backend with a body that contains a File data type, this error occurred: 

````
request entity too large
PayloadTooLargeError: request entity too large
```

After a quick googling, it turns out that there are a few things that I'll need to know about "uploading" files to a DB through an express backend. 

1. `multipart/form-data`
If you need to send files via a form, you'll need to add this flag to the form tag:
`enctype='multipart/form-data'`. 

The reason is because when you make a POST request, you have to encode the data that forms the body of the request in some way. 

There are 3 types of encoding: 
- `application/x-www-form-urlencoded` (default)
- `multipart/form-data`
- text/plain 

You just have to use the multipart encoding whenever you're sending file data. 

2. Using Multer
On the Express backend, you have to use the multer middleware to handle the request. Multer is a middleware built specifically for handling `multipart/form-data`, which is used primarily for uploading files. *Multer will not process any form which is not multipart.*

To use multer, you can do the following:
```javascript
const multer = require('multer')
const upload = multer({ dest: 'uploads/'})

app.post('/profile', upload.single('avatar'), (req, res, next) => {
    //req.file is the `avatar` file
    //req.body will hold the text fields, if there were any
})
```
That's it!

On the client form, you'll have to use a form that looks like this: 

```html
<form action="/profile" method="post" enctype="multipart/form-data">
  <input type="file" name="avatar" />
</form>
```

If you need to handle multiple files (an array), you can use the following:

```javascript
app.post('/photos/upload', upload.array('photos', 12), function (req, res, next) {
  // req.files is array of `photos` files
  // req.body will contain the text fields, if there were any
})
```

Now, one last thing. Notice at the beginning you specify an `upload` variable, with a `dest` property of 'uploads'. What is this? 

So, when the server receives the request, it'll need to save the file somewhere. This function specifies the folder in which the file is stored on the server's DiskStorage. 

That's about it!
