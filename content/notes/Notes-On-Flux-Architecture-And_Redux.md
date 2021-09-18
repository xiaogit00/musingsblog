---
title: "Notes on Flux Architecture And_Redux"
date: 2021-09-18T12:01:56+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Redux", "flux architecture"]
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
Okay this will be a summary of very important concepts that I've learned, which is the Flux architecture. The core idea is this.

There are three interconnected primitives within the flux architecture for state management. First, there is the reducer. A reducer is a function that takes in a state variable (similar to the 'useState' function), but in addition, also takes in an *action* variable. An action is simply an object that contains information regarding the user's action, to be enacted on the state. A common definition within action object is the `action.type` property.

During the initial definition of the reducer, the *effects* of the action on the state is outlined. For example, take a look at this code in the tutorial example where we have 3 simple ratings buttons that increment upon click.

```javascript
const initialState = {
  good: 0,
  ok: 0,
  bad: 0
}

const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'GOOD':
        const addGoodState = {
            ...state,
            good: state.good + 1
        }
      return addGoodState
    case 'OK':
        const addOkState = {
            ...state,
            ok: state.ok + 1
        }
        return addOkState
    case 'BAD':
        const addBadState = {
            ...state,
            bad: state.bad + 1
        }
        return addBadState
    case 'ZERO':
        console.log("state:", state)
      return initialState
    default:
        return state
  }

}

```

Notice how, each of the case returns a *new state* variable. A reducer is a *pure* function in that it never alters the state of the initial variable. In every instance that the reducer is invoked, you're creating a new state variable - which in this case, is addGoodState, or addOkstate.

In terms of project structure, the reducer also tends to be defined within the 'reducer' folder.

Next, is how the reducer is used within index.js - or anywhere that you need to perform an action to the state.

Introducing the second primitive of Redux: a store. A store is simply a *store of states*. You instantiate a store by passing the reducer that you've defined earlier, with the `createStore(reducer)` function. This needs to be imported into index.js with `import { createStore } from 'redux'`.

To put it into layman's terms, the reducer definition acts as a schema that tells the store *how you want to filter the action data that goes into the store*.

The reasoning will become clear when we consider the method by which we enact state change. We use `store.dispatch(action data)`. For instance, when we want to increment the good property of the state, we can do:

```javascript
//Within App component
const good = () => {
    store.dispatch({
      type: 'GOOD'
    })
  }

//JSX:
<button onClick={good}>good</button>
```
Upon click of the button, the good event handler is triggered, which dispatches this object into the store. The store, using the reducer that we've defined, returns a new state: addGoodState, which, in our case, is just the previous state with the good variable amended.

#### Implications for workflow.

Previously, we define the state using `const [exampleState, setExampleState] = useState([])`. To change the state, we call `setExampleState()`, and the app is rerendered. Now, things are a bit different. First, we create a *reducer*, which contains an initial state, and all the kinds of actions that would cause a change in state.

Second, we make a store using that reducer. Third, within our App component, we define event handlers that trigger state change using `store.dispatch()`.

Finally, by registering `store.subscribe(renderApp())` at the bottom like so:

```javascript
const renderApp = () => {
  ReactDOM.render(<App />, document.getElementById('root'))
}

renderApp()
store.subscribe(renderApp)

```
, we can rerender the app upon state change. Store.subscribe is called whenever there is a change in the state of the store.

So far, no problem. However, we can take this process even further.

Let's say, you've got a input field that you want to add notes to.

```javascript
const addNote = (event) => {
    event.preventDefault()
    const content = event.target.note.value
    event.target.note.value = ''
    store.dispatch({
      type: 'NEW_NOTE',
      data: {
        content,
        important: false,
        id: generateId()
      }
    })
  }
```

You register an event handler, that gets the content from the field, and dispatches it to the store. No problem. Similarly, if you want to toggle importance of a note, you dispatch some data to the store.

```javascript
  const toggleImportance = (id) => {
    store.dispatch({
      type: 'TOGGLE_IMPORTANCE',
      data: { id }
    })
  }
```
Within this approach, *we have not bound the state of the form fields to the state of the App component like we previously done*. If you recall, previously, the 'bounding' has been done like so:

The input field has an onChange handler, which is handleSearchChange. Every time I type a letter into field, the handler is triggered. Within the handler, the *state* of the *search* variable is updated via `setNewSearch(event.target.value)`, hence causing the app to rerender.

Thus, it is said that the state of the form field is bounded to the state of the app component. Every time the state of field changes, the component rerenders, and the state is set to thus.

Here, however, the fields are *not* bound. In other words, they're uncontrolled. The adding of notes is done when we *dispatch* it to the store, not like how it's done previously, by sending the constantly updated notes state. In other words, uncontrolled forms send only the end value, whilst 'controlled' forms send a value upon every state change.

Uncontrolled forms have limitations: dynamic error messages or disabling submit button based on input is not possible. Basically, any feature that requires 'real time' calculation of state is not possible when form fields are 'uncontrolled'. If they're controlled, it is 'smarter' in the sense that whenever a value changes, the validation responds to it.

Moving on.

A common practice is to further extract the actions into the reducer.js. For instance, not only does the reducer.js define the reducer, it will also have helper functions known as action creators. This is what they look like:

```javascript
export const createNote = (content) => {
    return {
        type: 'NEW_NOTE',
        data: {
            content,
            important: false,
            id: generateId()
        }
    }
}

export const toggleImportanceOf = (id) => {
    return {
        type: 'TOGGLE_IMPORTANCE',
        data: { id }
    }
}
```
After that is done, whenever we want to dispatch a piece of state, we simply have to do:
`dispatch(toggleImportanceOf(note.id)` or
`dispatch(createNote(content)`
within the event handlers in the components to alter the state. Code becomes cleaner overall.

### useDispatch() and useSelector()

Imagine a problem that I've faced before: to access a piece of state within a child component that are three layers deep, I have to end up passing the state as props down the chain three times from the parent component three layers above. It is a huge hassle.

We need a solution to access the state in the store in any component. One good way is to use the hooks api of the react redux library.

Steps for implementing global access to store:

#### Step 1: installing react-redux
`npm install react-redux`

#### Step 2: Within Index.js, (or nearest common ancestor), import Provider.
```javascript {hl_lines=[2]}
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider } from 'react-redux'import App from './App'
import noteReducer from './reducers/noteReducer'

const store = createStore(noteReducer)

ReactDOM.render(
  <Provider store={store}>    <App />
  </Provider>,  document.getElementById('root')
)
```
