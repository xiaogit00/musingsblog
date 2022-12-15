---
title: "New Mailmerge Workflow"
date: 2022-12-15T17:29:42+08:00
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

Alright the previous Mail merge workflow is shit. 

I found a new workflow, that leverages on the [python mailmerge package](https://github.com/awdeorio/mailmerge). 

Here's the new workflow:

1. Create your email database in Sheets, and download as csv. Use the following fields: `email`, `name`,etc.
2. Open Bash, `cd mailmerge`
3. Drag csv into folder, rename as `mailmerge_database.csv`
4. Edit `mailmerge_template.txt`
5. Run `mailmerge` to dry run. 
6. Run `mailmerge --no-limit` to dry run again
7. Run `mailmerge --no-dry-run`
8. Run `mailmerge --no-dry-run --no-limit`

Amazing. 