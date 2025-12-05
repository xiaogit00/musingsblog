---
title: "Streams App Documentation"
date: 2021-08-24T15:05:43+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
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
### Component Tree:

- App
  - LeftNav
    - NavigationMenu
      - NavHeader
      - NavSections : section
          - {section}-->|NavSection 1
          - {section}-->|NavSection 2
          - {section}-->|NavSection 3
            - {section.menuItems} (an array of objects)-->MenuItems |
              - Maps over: MenuItem
