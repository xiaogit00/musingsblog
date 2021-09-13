---
title: "Data Manipulation in Javascript"
date: 2021-09-13T11:21:29+08:00
# weight: 1
# aliases: ["/first"]
tags: ["javascript", "lodash", "data transformation", "sorting"]
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
Wow just from exercise 4.7 from FullStackOpen I've learned 3 very important and useful things within a one line solution.

This is the solution in question:

```Javascript
Object.entries(_.countBy(blogs, 'author')).sort((a,b) => {
    return b[1] - a[1]
})[0]
```

It took me several hours to figure out this exercise. This is because first I needed to get myself introduced to Lodash - and then, I got introduced to `Object.entries`, a way to convert an object into an array. Next, I needed to figure out how sort() works. Overall....I feel quite accomplished. But I'll just need to document everything I've learned from this exercise below.

### Background

Exercise 4.7 requires me to look through an array of blog objects, and find the author with the most number of posts. This is the raw data in question:

```Javascript
const blogs = [
    {
        _id: '5a422a851b54a676234d17f7',
        title: 'React patterns',
        author: 'Michael Chan',
        url: 'https://reactpatterns.com/',
        likes: 7,
        __v: 0
    },
    {
        _id: '5a422aa71b54a676234d17f8',
        title: 'Go To Statement Considered Harmful',
        author: 'Edsger W. Dijkstra',
        url: 'http://www.u.arizona.edu/~rubinson/copyright_violations/Go_To_Considered_Harmful.html',
        likes: 5,
        __v: 0
    },
    {
        _id: '5a422b3a1b54a676234d17f9',
        title: 'Canonical string reduction',
        author: 'Edsger W. Dijkstra',
        url: 'http://www.cs.utexas.edu/~EWD/transcriptions/EWD08xx/EWD808.html',
        likes: 12,
        __v: 0
    },
    {
        _id: '5a422b891b54a676234d17fa',
        title: 'First class tests',
        author: 'Robert C. Martin',
        url: 'http://blog.cleancoder.com/uncle-bob/2017/05/05/TestDefinitions.htmll',
        likes: 10,
        __v: 0
    },
    {
        _id: '5a422ba71b54a676234d17fb',
        title: 'TDD harms architecture',
        author: 'Robert C. Martin',
        url: 'http://blog.cleancoder.com/uncle-bob/2017/03/03/TDD-Harms-Architecture.html',
        likes: 0,
        __v: 0
    },
    {
        _id: '5a422bc61b54a676234d17fc',
        title: 'Type wars',
        author: 'Robert C. Martin',
        url: 'http://blog.cleancoder.com/uncle-bob/2016/05/01/TypeWars.html',
        likes: 2,
        __v: 0
    }
]
```

#### Initial hunch:
I first thought that I'll need to groupBy the data. Using lodash's groupBy method `_.groupBy(blogs, (blog) => blog.author )`, I arrived at the following solution:

```javascript
{
      'Michael Chan': [
        {
          _id: '5a422a851b54a676234d17f7',
          title: 'React patterns',
          author: 'Michael Chan',
          url: 'https://reactpatterns.com/',
          likes: 7,
          __v: 0
        }
      ],
      'Edsger W. Dijkstra': [
        {
          _id: '5a422aa71b54a676234d17f8',
          title: 'Go To Statement Considered Harmful',
          author: 'Edsger W. Dijkstra',
          url: 'http://www.u.arizona.edu/~rubinson/copyright_violations/Go_To_Considered_Harmful.html',
          likes: 5,
          __v: 0
        },
        {
          _id: '5a422b3a1b54a676234d17f9',
          title: 'Canonical string reduction',
          author: 'Edsger W. Dijkstra',
          url: 'http://www.cs.utexas.edu/~EWD/transcriptions/EWD08xx/EWD808.html',
          likes: 12,
          __v: 0
        }
      ],
      'Robert C. Martin': [
        {
          _id: '5a422b891b54a676234d17fa',
          title: 'First class tests',
          author: 'Robert C. Martin',
          url: 'http://blog.cleancoder.com/uncle-bob/2017/05/05/TestDefinitions.htmll',
          likes: 10,
          __v: 0
        },
        {
          _id: '5a422ba71b54a676234d17fb',
          title: 'TDD harms architecture',
          author: 'Robert C. Martin',
          url: 'http://blog.cleancoder.com/uncle-bob/2017/03/03/TDD-Harms-Architecture.html',
          likes: 0,
          __v: 0
        },
        {
          _id: '5a422bc61b54a676234d17fc',
          title: 'Type wars',
          author: 'Robert C. Martin',
          url: 'http://blog.cleancoder.com/uncle-bob/2016/05/01/TypeWars.html',
          likes: 2,
          __v: 0
        }
      ]
    }
```
The array is transformed into an object with each group item as key, and its associated objects as values, stored in an array. This in itself is a massively useful function, I feel.

However, I stumbled into the problem of being unable to extract or 'count' how many items are there in each group. What am I supposed to do, loop over each key-value pair of the object, and then have a counter track the number of values within an array? It all seems a little too complicated for what I need.

Then I started Googling and discovered that the right thing to use would probably be the countBy method.

The countBy method of the lodash library takes the object in question, as well as the attribute you want to count. In my case, it is `countBy(blog, 'authors')`. This magically returns this:

```Javascript
{ 'Michael Chan': 1, 'Edsger W. Dijkstra': 2, 'Robert C. Martin': 3 }
```

Perfect - I am one step closer to my solution. At this point, however, I still needed a way to sift through each key-value pair and return the highest value. I can't map or reduce since it's an object (or can I?). In any case, I found out that by using `Object.entries()` I could turn an object into an array of its key-value pairs. From there, I could then filter out what I needed more easily.

```javascript
//after Object.entries()
[
    [ 'Michael Chan', 1 ],
    [ 'Edsger W. Dijkstra', 2 ],
    [ 'Robert C. Martin', 3 ]
]

```

Here, I could either do a loop and an inner loop with a counter - or, I've decided that it's even simpler to just use the sort() method. Problem is, the sort() method within javascript seems a little more verbose than other libraries I've used (like pandas...). The full syntax of the sort method ended up like this (which is actually very common):

```Javascript
array.sort((a,b) =>{
    return b[1]-a[1]
})
```
Sort takes a call back function, which iterates through each item in the array. `a` refers to the nth array, whilst `b` refers to the n+1th array. Here, what i am basically saying is that, take b[1] - a[1]. If it is positive, b is sorted ahead of a. Under the hood, sort iterates through each item in the array, and uses compare function. Compare takes a-b If the result of compare function is negative, a is sorted before b. If it is postiive, b is sorted before a.

And viola. Putting it all together, I get:

```javascript
Object.entries(_.countBy(blogs, 'author')).sort((a,b) => {
    return b[1] - a[1]
})[0]
```

which returns me: `[ 'Robert C. Martin', 3 ]` -- the data that I needed.

---

Throughout all these, I've also began using a different developmental workflow that involves unit testing. Instead of coding and running the entire app, I've separated each piece into its own function, and ran tests to check whether each function works. I am liking this workflow better as it ensures modular confidence...To put it in the words of the instructor:

> refactor your application in baby steps and verify that the application works after every change you make. If you try to take a "shortcut" by refactoring many things at once, then Murphy's law will kick in and it is almost certain that something will break in your application. The "shortcut" will end up taking more time than moving forward slowly and systematically.

Similarly, I shouldn't try to code complex architectures without ensuring that modular pieces are working fine...
