---
title: "On the URL Method of Vanilla JS"
date: 2021-12-13T17:26:17+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Vanilla JS", "HTTPS"]
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
So recently I've discovered a weird construct in Vanilla JS that I haven't seen before: the URL method. It looks like something like this:

```javascript
const url = new URL(`${covalentAPI}${APIEndpoint}/`);
url.search = new URLSearchParams({
    key: APIKEY,
    tickers: tickers
})
```

So after googling a little bit, I figured out what it does.

### URL method
The URL method creates a new URL object. You can think of an URL object as a more convenient way of extracting and using different parts of URLs. The URL object has the following attributes:


| Attribute        | Example  |
| ------------- |:-------------:|
| href    | https://api.covalenthq.com/v1/pricing/tickers/   |
| origin     | https://api.covalenthq.com       |
| password | *not in example, but returns a string containing the password specified in the domain name* |
| pathname     | /v1/pricing/tickers/  |
| port      | *returns the port number*        |
| protocol | https:      |
| search     | ?key=ckey_docs&tickers=BTC%2CWETH%2CDAI%2CAAVE  *begins with the leading ? character* |
| searchParams     | *object used to access individual query params*     |
| username | *string containing username specified in domain name*        |


### Creating new URL object
To create a new URL object, you use `new URL()`, supplying either an absolute URL, or a relative URL string and a base URL string.

#### Absolute:
`const url = new URL('https://example.com/323232')`

#### Relative:
`const url = new URL('../cats', 'http://www.example.com/dogs'`  
`console.log(url)`
--> `url = http://www.example.com/cats`

### URLSearchParams
Within the code, there is a `URLSearchParams()` method that is quite mysterious.

```javascript
url.search = new URLSearchParams({
    key: APIKEY,
    tickers: tickers
})
```

In this case, we're creating a new URLSearchParams object, and assigning its value to `url.search`.

Before this assignment, `url.search` returns empty. After the assignment, `url.href` returns:
```html
https://api.covalenthq.com/v1/pricing/tickers/?key=ckey_docs&tickers=BTC%2CWETH%2CDAI%2CAAVE
```
while url.search returns:
```html
?key=ckey_docs&tickers=BTC%2CWETH%2CDAI%2CAAVE
```

That's it! The URL object is just an easier way for us to manipulate and extract GET request information, a handy little tool for API based calls. 
