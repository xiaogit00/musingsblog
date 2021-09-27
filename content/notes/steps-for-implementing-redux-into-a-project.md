---
title: "Steps for Implementing Redux Into a Project"
date: 2021-09-24T15:33:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Redux"]
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


### Step 1: Install Redux & react-redux, and redux-thunk
`npm install redux`  
`npm install react-redux`
`npm install redux-thunk`

### Step 2: Think about what states you want your store to have
For my case, it was:
```javascript
{
    streams: [{},{}]
	trades: [{},{}]
	globalNominalDenom: 'SGD'
}
```

### Step 3: Taking stock of ways Database data and store data is interacted with  
There are three broad functions you're trying to achieve, what I call, the right hand, left hand, and the reducers. Right hand is in charge of getting state from database. Left hand is in charge of 'pushing' state to store via dispatch. With the help of redux-thunk, you essentially combine these two functions together into the definition of action creators.
![](https://res.cloudinary.com/dl4murstw/image/upload/v1632724547/Screenshot_2021-09-27_at_2.35.37_PM_kuwbl3.png)

So, given an empty project, first outline all the ways the data will be interacted with. This will correspond to the available routes in your project.

For streams, for instance, I have
- GET
- POST
- PUT (:/id)
- PUT(:/id/trade)
- DELETE

For trades, I have:
- GET
- POST
- PUT
- DELETE

## Laying the groundwork
### Step 4: Creating store.js and applying thunk Middleware
We'll first by creating the style in Invocation Driven Development fashion. Creating the store early helps us test the rest of the code during development.

```javascript {hl_lines=[1,3, 18]}
import { createStore, combineReducers, applyMiddleware  } from 'redux'
import thunk from 'redux-thunk'
import { composeWithDevTools } from 'redux-devtools-extension'

import anecdoteReducer from '../reducers/anecdoteReducer'
import notificationReducer from '../reducers/notificationReducer'
import filterReducer from '../reducers/filterReducer'

const reducer = combineReducers({
    notification: notificationReducer,
    anecdotes: anecdoteReducer,
    filter: filterReducer
  })

const store = createStore(
    reducer,
    composeWithDevTools(
      applyMiddleware(thunk)
    )
)

export default store
```
### Step 5: Make changes to index.js to supply store.
```javascript {hl_lines=[3,4,9]}
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import store from './utils/store'
import App from './App'


ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

### Step 6: Make changes to App.js to initialize state of store
Don't worry about `dispatch(initializeAnecdotes())` not working for now, we'll implement the action creator `initializeAnecdotes` shortly.

```javascript
import React, {useEffect} from 'react'
import { initializeAnecdotes } from './reducers/anecdoteReducer'
import { useDispatch } from 'react-redux'
const App = () => {
  const dispatch = useDispatch()
  useEffect(() => {
    dispatch(initializeAnecdotes())
  }, [dispatch])
```

## Step 7: Main steps: Code the Reducers  

But before we begin, we need to ask ourselves:
#### Higher level logic
- What kind of actions would I need? Think about the labels you want to set their type to.  

**Streams**
- GET (api/streams) :  `'INIT_STREAMS'`
- POST (api/streams) : `'NEW_STREAM'`
- PUT (api/streams) : `'UPDATE_STREAM'`
- PUT (api/streams/:id/trade): `'UPDATE_TRADE_IN_STREAM'`
- DELETE (api/streams): `'DELETE_STREAM'`

Thus, streamReducer will contain 5 cases.

**Trades:**
- GET (api/trades) :  `'INIT_TRADES'`
- POST (api/trades) : `'NEW_TRADE'`
- PUT (api/trades) : `'UPDATE_TRADE'`
- DELETE (api/trades): `'DELETE_TRADES'`

## Step 8: Implementation (one reducer at a time)
#### a) In frontend of app, create a reducers folder.  
#### b) Create two files: streamReducer.js, tradeReducer.js  
#### c) Implement streamReducer and dependencies (action creators and services) below:
#### d) Code the outline for streamReducer  
```javascript
const streamReducer = (state = [], action) => {
    switch(action.type) {
        case 'INIT_STREAMS': {

        }

        case 'NEW_STREAM': {

        }

        case 'UPDATE_STREAM': {

        }

        case 'UPDATE_TRADE_IN_STREAM': {

        }

        case 'DELETE_STREAM': {

        }
    default:
        return state
    }
}
```
#### e) Code reducer dependencies 'top-down' from dispatch() call  

Then, think about the cases that you'd want to dispatch() to store, and code the corresponding action creators, using what I call 'Invocation Driven Development' (IDD)

##### 1) First point of invocation (within App.js)  
dispatch call:
```javascript
dispatch(initializeStreams())
```
Okay, you need an action creator.
##### 2)  action creator (within streamsReducer.js):
```javascript
const initializeStreams = () => {
    return async dispatch => {
        const streams = await streamService.getAll()
        dispatch({
            type: 'INIT_STREAMS',
            data: streams
        })
    }
}
```
You'll realize that you'll code your way into the dependency, `streamService`.  
##### 3) streamService.js  
Create a folder called `services`. Create a file called 'streams.js'
streams.js:  
```javascript
import axios from 'axios'

const baseUrl = 'http://localhost:3003/api/streams'

const getAll = async () => {
  const response = await axios.get(baseUrl)
  return response.data
}

export default { getAll }
```

Within streamReducer.js, import streamService
`import streamService from '../services/streams'`

#### f) Do the same for rest of the actions: think about how action is called, define action creator, code the service.

>This is tricky, because you're updating 4 documents at once - place it's invoked (component), Action Creator(reducer), Axios Service(service/streams.js), Reducer(streamReducer.js) - in an iterative fashion.

Example: POST  
*How it's called:*
```javascript {hl_lines=[3]}
//within NewStream component event handler
const content = event.target.stream.value
dispatch(newStream(content))
```
*Defining Action Creator:*
```javascript
//within streamReducer.js
const newStream = content => {
    return async dispatch => {
        const newStream = await streamService.createNew(content)
        dispatch({
            type: 'NEW_STREAM',
            data: newStream
        })
    }
}
```

*Coding the service:*
```javascript
//within services/stream.js

const createNew = async (content) => {
    const response = await axios.post(baseURL, content)
    return response.data
}
```

Then, fill in more for your reducer:
```javascript {hl_lines=["7-9"]}
const streamReducer = (state = [], action) => {
    switch(action.type) {
        case 'INIT_STREAMS': {
            return action.data
        }

        case 'NEW_STREAM': {
            const newStreams = state.concat(action.data)
            return newStreams
...
}
```
---
One more example: UPDATE:  
*How it's called:*
```javascript
dispatch(updateStream(id, content))
```

*Defining Action Creator:*
```javascript
const updateStream = id, content => {
    return async dispatch => {
        const newStream = await streamService.updateStream(id, content)
        dispatch({
            type: 'UPDATE_STREAM',
            data: {
                id,
                content: newStream
            }
        })
    }
}
```

*Coding the service:*
```javascript
const updateStream = async (id, newStream) => {
    const response = await axios.put(`${baseURL}/${id}`, newStream)
    return response.data
}
```
*Filling in the reducer*
```javascript
const streamReducer = (state = [], action) => {
    switch(action.type) {
...
        case 'UPDATE_STREAM': {
            const id = action.data.id
            const newStream = action.data.content
            return state.map(stream => stream.id !== id ? stream : newStream)
        }
...
}
```

## Step 9: Things to note about this architecture:
1. The right hand, axios, is entirely embedded into the Action Creators, and called via *services*.

2. The left hand, store alteration, is also embedded into Action creators.

3. Each of the Action handlers within Reducer has its own unique logic. INIT uses getAll and returns state. POST uses createNEW service, which itself uses axios.post. UPDATE_STREAM creates a new stream object, and maps over state to 'replace' the objected associated with the targetted ID.

And importantly:

4. The function of reducers is to alter left hand. Right hand data is altered with one line within action creators solely, before LH data is altered. This is redux thunk logic.

## Step 10: Do the same for all actions in all states, testing them one step at a time.
