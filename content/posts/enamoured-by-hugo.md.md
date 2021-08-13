---
title: "Enamoured by Hugo"
date: 2021-08-13T18:22:56+08:00
# weight: 1
# aliases: ["/first"]
tags: ["blogging", "stack"]
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
Unrelated post, but I am pretty much enamoured by Hugo.

I built this site using a simple Hugo theme and I am now hosting it on Netlify. I have been an avid blogger (well, privately for the most part of it) for close to 10 years, and this is the first time that I am writing my blog from a text editor like Atom, and *pushing it into Github to update*. The workflow is significantly different from CMS sites like Wordpress and Blogger (long time fan, still use it after its death in the hands of Google), but after my experience for the last few days I am seriously enamoured.

Not only is the website extremely zippy, beautiful, and responsive, the updating process is also rather seamless once you set up some shortcuts on the terminal. It takes me two commands[^1] from my terminal to create a new post and propagate it to the web. But more than that - I think what really sold it for me is the ability to leverage on the inbuilt features of Hugo markdown editing to create amazing layouts for different types of needs. Inline code `can be included like this`. Multiline blockcodes can be written in any language and rendered as these:

```javascript
//this is a section in javascript
const Header = () => {
  setTitle();
}
```

```python
# this section is in Python
def timesTwo(x):
  x = x * 2
  return x
```

In addition, I can also include blockquotes like so:

>To be or not to be, is not truly a question

All in all, I am practically sold on the Jam stack. It just seems so...fast and lightweight and uncumbersome.

And if I need to embed a video or a tweet, I can do so easily:

{{< youtube id="hEAl3QTkehc" autoplay="true" >}}

{{< twitter 1425785212592541702 >}}

I began writing this post at 6:26, now it's 6:43 and I am done with it.

Amazing. To great days writing ahead.

p.s. oh and I got this domain for $1 on namecheap btw. :relaxed:

[^1]: If any of you are interested, I've basically set up some functions on zsh terminal on my mac so that I chain a couple of commands. One of them is newpost (which creates a new post - it takes an argument `post-slud.md` which Hugo automatically sets as the page URL as well as title), and the other one is gitpush (you can guess which set of commands this does - serious devs don't kill me).
