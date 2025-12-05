---
title: "Notes on Multiple States in Redux Store"
date: 2021-09-21T07:57:00+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Redux", "State Management"]
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
## Basic Idea of Multiple States
Quite naturally, the store within Redux should be able to store multiple kinds of states. Consider the following state:
```javascript
{
  notes: [
    { content: 'reducer defines how redux store works', important: true, id: 1},
    { content: 'state of store can contain any data', important: false, id: 2}
  ],
  filter: 'IMPORTANT'
}
```
On a top level, Redux architecture allows us access to a 'global' state. Each state is a property within the Redux store.

The main thing to note is that each piece of state, each property, is altered using a reducer. The filter reducer looks like this:

``` javascript
const filterReducer = (state = 'ALL', action) => {
  switch (action.type) {
    case 'SET_FILTER':
      return action.filter
    default:
      return state
  }
}
```

### Importantly, all the reducers that we create need to be combined before passing into the store.

Therefore, there is *only one store*. Before we `createStore()`, we need to `combineReducers()`.

We can combine reducers like so:
```javascript {hl_lines=["3-6"]}
import { createStore, combineReducers } from 'redux'

const reducer = combineReducers({  
    notes: noteReducer,  
    filter: filterReducer}
)

const store = createStore(reducer)

```
The combineReducers() function that we import from 'redux' module accepts an object with keys for the state you want to create, and the reducer for the value of the property. Then, we create store.

That's about it. Pretty simple!

One last thing about the behavior of multiple states. Whenever an action is sent to reducer with multiple combined states, the action "gets handled in every part of the combined reducer". Meaning, the action will flow through the first reducer, before flowing through the second one. Typically, only one reducer is interested in the action.
