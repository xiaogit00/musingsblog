---
title: "Regex Problem"
date: 2023-09-06T16:16:05+08:00
# weight: 1
# aliases: ["/first"]
tags: ["regex"]
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
So whilst working on my Python, I stumbled upon this rather tricky Regex problem that took me a long time to solve. The problem is as follows:

```
How would you write a regex that matches a number with commas for every three digits? It must match the following:
• '42'
• '1,234'
• '6,368,745'
but not the following:
• '12,34,567' (which has only two digits between the commas)
• '1234' (which lacks commas)
```

The difficult part of this problem is making sure that your regex manages to match everything under 1000, which dictates something like `\d{1-3}`, but at the same time, this logic fades into irrelevance once the number passes 1000 for instance in the case of `'1,234'`. A thought I had was: how do you include conditional logic into Regex itself? Like, if the number is more than 1000, then the RHS parts of the regex cannot be 1-3 digits, but *must* be `\d{3}`. And so, I tried incorporating conditional logic into my regex like so, the final product of which was:
```
numRegex = re.compile(r'''(
            (\d{1,3}), # Case between 1 M to 10k
            ((\d{3}),)+
                (\d{3})
                |
            (\d{1,3}),
                (\d{3}) #case between 10k to 1k
                |
                (\d{1,3}) # Case of less than 1000
                )''', re.VERBOSE)
```
Even so, this ugly monstrosity (which catches 3 different kinds of cases, for terms under 1k, for terms betweek 10k to 1k, and for terms from 1M to 10k and above) fails to *exclude* the case of '12,34,567'. It returns me `12` lol. And that's because, the third condition actually catches the *first* part of '12,34,567'. The whole thing was a mess and I spent way too long on this problem so I threw the towel in and asked ChatGPT. 

The answer ChatGPT returned me was simple, and works:
`^\d{1,3}(,\d{3})*$`

So the explanation is, the `^` and `$` 'anchors the regex to the start and end of the string to make sure it matches the entire string'. In this case, putting a `$` at the end tells the regex to only match strings that end with 3 digits - if it exists, *along with the comma*. If, in the case of `123` the comma does not exist, then there's no need to match 3 digits - the front part satisfies. The `^` ensures that you match 1-3 digits at the start of the string. If I just did `\d{1-3}`, it might match '12,34,567' and return only '34,567'! So that is not what you want. Essentially adding the carrot ensures that you don't only match a later part of the string that fulfils the match criteria when the whole thing should have been excluded. 

The latter part `(,\d{3})*` matches 0 or more of exactly 3 digits. This is way more elegant than how I thought of it - I grouped the comma in the first part. 

An important lesson of working with Regex seems to be:  
**being able to spot the pattern in the underlying structure**. 

In this case, if I can spot that a number `12,345,678` can be composed of 2 kinds of patterns, 1. a pattern at the start of the string that may vary between 1 to 3 digits, and that this must be at the start of the string; and 2. a pattern after that that repeats itself, with a comma, and must contain 3 digits, and may or may not exist, then I'll be able to model this logic using a combination of all the regex symbols. 

ChatGPT also answered it perfectly:
```
In the original regex pattern ^\d{1,3}(,\d{3})*$:

^\d{1,3} matches one to three digits at the start of the string.  
(,\d{3})* matches zero or more occurrences of a comma followed by exactly three digits.  
$ ensures that the pattern must match the entire string, from start to finish. This means that there should be no extra characters before or after the matched number with commas.
```