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

1. Open Bash, `cd mailmerge` && `code .`
2. Go to `mailmerge_database.csv` and paste the info inside from whereever. Use the VSCode csv plugin.
3. Edit `mailmerge_template.txt` or pick the right template
4. Run `mailmerge` to dry run. 
5. Run `mailmerge --no-limit` to dry run again
6. Run `mailmerge --no-dry-run`
7. Run `mailmerge --no-dry-run --no-limit`

Amazing. 