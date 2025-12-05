---
title: "Uploading Binary Files Into MongoDB"
date: 2023-05-19T16:55:00+08:00
# weight: 1
# aliases: ["/first"]
tags: ["binary", "mongodb", "backend"]
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

So part of my requirements for Kotakit was that I needed to upload PDFs into MongoDB. 

And I've never done that before. 

After googling, it turns out that for files less than 16mb, I could simply upload them as binary files. 

I also know next to nothing about the binary format. 

After an afternoon of tinkering around, I've finally managed to upload a binary file into my local instance of Mongodb. So here's an attempt to note down everything that I've learned about the process and what is required. 

1. So the basics set up has already been documented in [this previous post](https://www.musingsondefi.xyz/notes/uploading-files-to-express/). The gist of it is, in the frontend form itself, I'll need to specify that it's a multi-part form data, and then on the backend, I'll use Multer. Within the backend route, I'll then be able to retrieve the JSON part of the form data with `req.body`, and the file part of the form data with `req.file`. Great. Oh, and also, when you specify that Multer uploads it into the `dest: 'uploads/'`, it literally prints the file into an upload folder in the backend project. This is not what I want. 

2. It turns out that I don't want to upload it into diskstorage. Rather, I'll want to store it in memory, and then upload them directly onto MongoDb. To do that, I'll need to define the upload object as memory storage, like so: `const upload = multer({ storage: storage })`. When I console.log the `req.file` object, it correctly prints:

```javascript
{
  fieldname: 'file',
  originalname: 'receipt.pdf',
  encoding: '7bit',
  mimetype: 'application/pdf',
  buffer: <Buffer 25 50 44 46 2d 31 2e 34 0a 25 d3 eb e9 e1 0a 31 20 30 20 6f 62 6a 0a 3c 3c 2f 43 72 65 61 74 6f 72 20 28 4d 6f 7a 69 6c 6c 61 2f 35 2e 30 20 5c 28 4d ... 109066 more bytes>,
  size: 109116
}
```

A couple of things to note here. The req.file object contains properties `fieldName` (the name of the field that I defined on the frontend), `originalName` (the file name), the `mimetype` (it seems to be the document type), the `buffer` (binary data), and the `size` (109kb). 

3. The next big challenge I had was actually saving the file into Mongodb. When I tried saving it, the `file` property simply did not appear in the record. 

In my schema, I've defined my file field as a direct Buffer object: `file: Buffer`:

```javascript {hl_lines=[13]}
const spatialLayoutSchema = new mongoose.Schema({
    name: String,
    projectId: {
        type: mongoose.Schema.Types.ObjectId, 
        ref: 'Project'
    },
    clientId: [{
        type: mongoose.Schema.Types.ObjectId, 
        ref: 'Client'
    }],
    isSigned: Boolean,
    isFinal: Boolean,
    file: Buffer,
    dateSigned: Date,
    dateUploaded: Date,
    dateConfirmed: Date
})
```

The result is that the document is saved to Mongodb, but without the `file` field:
```JSON
{
  "_id": {
    "$oid": "6467412e8a950103c90bfc61"
  },
  "name": "receipt.pdf",
  "projectId": {
    "$oid": "e71f57b9b6b23ddce91f829f"
  },
  "clientId": [],
  "dateUploaded": {
    "$date": "2023-05-19T09:28:14Z"
  },
  "__v": 0
}
```

Up till this point I still don't know why, but after changing the `file` schema to the following: 

```javascript
file: {
        data: Buffer, 
        contentType: String, 
        fileName: String
    },
```

And the creating of that document to:

```javascript
const document = new SpatialLayout({
      ...body,
      file: {
        data: req.file.buffer,
        contentType: req.file.mimetype,
        filename: req.file.originalname,
      }
    })
```

I did manage to save the record in the DB. And it looks like this:

```JSON
{
  "_id": {
    "$oid": "6467439e4fa92cad2490b419"
  },
  "name": "receipt.pdf",
  "projectId": {
    "$oid": "e71f57b9b6b23ddce91f829f"
  },
  "clientId": [],
  "file": {
    "data": {
      "$binary": {
        "base64": "JVBERi0xLj...very long string",
      },
    },
    "contentType": "application/pdf",},
}

```

So what can I observe from this? The actual record that is saved in Mongodb database has the fields: data, and contentType. Instead of simply saving the buffer object directly under the `data` property, it is saved under the `$binary` field, under a `base64` field. 

I got it from [this tutorial](https://www.geeksforgeeks.org/upload-and-retrieve-image-on-mongodb-using-mongoose/) that I should be saving the schema of that field in that way. 

### Takeaways

In order to save binary files into Mongodb properly, it seems like the schema you need to define for the file **must be an object**:

```javascript
file: {
        data: Buffer
    },
```

It is entirely ok to leave out the other fields (I tested), but the data field must be within an object. I don't know where I can find this rule, but it must be somewhere within the Mongoose documentation. 

Doing so saves the buffer file properly. 

### Next steps
Now, the next step would be to retrieve and display that file on the frontend. Let's try to do that now. 

The problem is, it seems like now I am reading that it's normally not advised to store the binary files directly onto the database. According to [this article](https://www.datanamic.com/support/storeimagesinthedatabase.html): 

*Storing the image data inside a binary field leaves that data only available to an application that streams raw image data to and from that field. You cannot view the image with an external standard image viewer anymore.*

It does make sense. Seeing that huge chunk of binary file means that only my application will be able to stream that raw image data from that field. It also means performance overheads getting records from that database. 

According to [this source](https://www.reddit.com/r/learnprogramming/comments/uc0jj2/do_i_need_to_use_cloudinary_in_order_to_store/):

*It is normally not advised to try and store images in a database. The idea is that you host them with some provider (Cloudinary, AWS etc) and then just reference the URL in the database column.*

And [this](https://www.quora.com/Should-I-store-my-data-in-a-database-or-should-I-just-store-the-PDF-documents-that-I-will-eventually-create-on-the-server): 
*Store the PDFs on the file system (as its designed to store files) and let the database store data. The performance hit of storing files inside the DB gets to be prohibitive on a number of levels including queries, back ups become larger, hard disk space concerns etc.*

*Generate and store the file on the filesystem. Include a hash of the data with the record, so if the data changes you can trigger a new file generation replacing the old file.*

Hmm. Turns out this is actually quite a complex and deep topic. Way too deep at the level of this simple MVP. But for reasons for simplicity, I will not store my files as blob. 

Also, according to my research, I should be looking into Amazon s3. Going to figure that out and put it into the next post. 