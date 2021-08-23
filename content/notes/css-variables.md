---
title: "Css Variables"
date: 2021-08-23T08:18:26+08:00
# weight: 1
# aliases: ["/first"]
tags: ["css", "notes"]
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
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "https://res.cloudinary.com/dl4murstw/image/upload/v1629680284/Screenshot_2021-08-23_at_8.57.58_AM_sgxqnq.png" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
Whilst I was scrolling through Reddit, I noticed this line in the [css file for a super slick input field](https://leongeldsch.github.io/css_material_input/):

```css
:root {
    --main-blue: #90caf9;
    --main-white: #fff;
    --main-grey: rgba(255, 255, 255, 0.23);
    --main-dark-grey: #121212;
    --main-ease: cubic-bezier(.65,.05,.36,1);
}
```

And so I googled what on earth is the `:root` selector, and what on earth are the --main-blue syntax. What I learned was *incredibly* useful.

`:root` is a pseudo-class[^1] that matches the root element in the document - in most cases, it is <html>. However, due to its higher specificity[^2], `:root` selector is often used to declare global CSS variables.

Global CSS variables are variables used throughout the text. They're set like so: `--main-color:black` and are accessed using var() function: `color: var(--main-color)`.

Super useful stuff.

Within the CSS code of the input field above, we see an example of how the coder set the color palette of the entire site via the :root selector.

---

Footnotes

[^1]: a pseudo-class is a keyword added to the selector that specifies the special state of the selector. E.g. :hover can be used for actions upon hover in

```css
button:hover {
  color:blue
}
```

[^2]: Specificity refers to how the browser selects which CSS to be applied. It is a weight that is applied to a given CSS declaration. The following increases by specificity:
1. type selector (e.g. h1)
2. class selector (.bottom)
3. ID selector
