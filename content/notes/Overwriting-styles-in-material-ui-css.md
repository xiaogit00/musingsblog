---
title: "Overwriting Styles in Material Ui Css"
date: 2021-09-07T12:35:36+08:00
# weight: 1
# aliases: ["/first"]
tags: ["notes", "material ui"]
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

This was a tricky problem that I took a while to wrap my head around and implement.

Basically, I needed to change the font and background-color of a material UI button, when I hover over it.

The standard documentation was mainly to use className to change - which did nothing to the *selected* property. Because the specificity is lower. Using external CSS also fails due to lower specificity.

After fiddling around, I realized that this is the only way:

### Step 1:
I'll need to define  `makestyles` as such:

```javascript
const useStyles = makeStyles({
  root: {
    '&$selected': {
      color: "blue"
    }
  },
  selected: {} //Here, selected uses a jss-nested plugin to reference a local rule
})//    within the same stylesheet.
```
basically this compiles to `.root-x.selected-x {color: blue;}`; or, in my case, `.makeStyles-root-1.makeStyles-selected-2`, whose specificity is *higher* than the default one which is `.MuiToggleButton-root.Mui-selected`.

### Step 2:
It's crucial that I include *both* generated DOM names, root and selected
```javascript
<ToggleButton value="left" aria-label="left aligned"
classes={{
  root:classes.root,
  selected: classes.selected
}}
>
```

For reference: the classes prop of the MUI component *'use classes to provide an object that maps name of classes to override (.MuiToggleButton-root.Mui-selected) to css class names to apply. class.whatever (defined above)'*
