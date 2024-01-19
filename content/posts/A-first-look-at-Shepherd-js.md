---
title: "A First Look at Shepherd Js"
date: 2024-01-15T14:27:35+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Onboarding", "fullstack", "Shepherd.js"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: true
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
Recently I have completed my investment tracking web app, streamstrack.xyz, but I figured that it might not be very intuitive for users to use. So I began looking into the possibility of creating an onboarding flow. 

I've designed the onboarding screens out in Figma, but realized that for me to build a modal flow using CSS and Javascript would be quite a pain in the ass. It'll involve tracking many different UI states and creating custom modal components that may or may not fit properly into the right places on the screen especially after you resize them. The core difficulty is trying to overlay each modal component near or next to some other element on screen, but have it adjust in the same way to responsive window resizing. So after thinking about it, I've decided that I'll probably just use some onboarding libraries. I was surprised to find that they exist. Here's three top candidates based on my research: 

1. intro.js
2. joyride
3. shepherd.js

Shepherd.js's landing page looks the most aesthetically pleasing, so I've decided to explore that first. Let's see what it's all about. 

### A first look at Shepherd.js
So it's great that Shepherd has a [react-wrapper](https://github.com/shepherd-pro/react-shepherd). Let's create a random project and play around with it a little, before testing it out on my app. 

## How does Shepherd work??
So first, you define a tour context by using ShepherdTourContext. 

Ok so you create a button, onClick, you trigger tour.start. 

In the example app, You have a wrapper component called ShepherdTour, which takes 'steps' component, with `tourOptions`. 

Hmm. The React abstraction is a bit confusing. Going to reference the JS version first. 

#### How shepherd.js works
First, you create a new tour instance. 

```javascript
const tour = new Shepherd.Tour({
  useModalOverlay: true,
  defaultStepOptions: {
    classes: 'shadow-md bg-purple-dark',
    scrollTo: true
  }
});
```

Then, you add your steps. 

```javascript
tour.addStep({
  id: 'example-step',
  text: 'This step is attached to the bottom of the <code>.example-css-selector</code> element.',
  attachTo: {
    element: '.example-css-selector',
    on: 'bottom'
  },
  classes: 'example-step-extra-class',
  buttons: [
    {
      text: 'Next',
      action: tour.next
    }
  ]
});
```

Third, you can call start by calling `tour.start()` -> which, in my case, I've attached to a button. 


What appears on screen, seems to be this: 

![shepherd][https://res.cloudinary.com/dl4murstw/image/upload/v1705305568/shepherd-ezgif.com-optimize_ozp85z.gif]

Let's break down what happens on the screen when we press start. 

First, it seems like a new class is added to the body `shepherd-enabled shepherd-target`. Then, a new div is injected into the dom, with the class of `shepherd-element example-step-extra-class`. It says in the dialog that it's added to the `example-css-selector` element, but I don't see that anywhere in the css at all...rather, it seems to be the last div of the `shepherd-content` class. 

At the very top, is that black box, which is the `svg` elem `shepherd-modal-is-visible` and `shepherd-modal-overlay-container`. 

Okay. 

It seems like first of all, that each step is added iteratively by calling `.addStep`, and you can specify the following things: 
- id
- text
- attachTo
- classes
- buttons (at the footer)

If this is the case, let's experiment with a couple of things. Let's say I have a flexbox with many items, and I want to start a tour that takes me through each of those items. 

How do I center the modal on Menu1, or design it? 