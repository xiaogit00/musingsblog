---
title: "Understanding Zustand and Immer"
date: 2023-01-10T06:45:04+08:00
# weight: 1
# aliases: ["/first"]
tags: ["zustand", "immer", "useCallback"]
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
So, I've previously written about Zustand before, and implemented it with Immer to get and set simple states, but I realized my understanding of it is not deep enough for me to implement cases where I need to set nested states. The purpose of this little write up is to figure out what exactly is going on with Zustand and Immer, so that I can achieve my goals. 

## Understanding Zustand
Using Zustand is simple. All you need to do is to create a store

```javascript 
const useBearStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```
and use it: 

```javascript
const increasePopulation = useBearStore((state) => state.increasePopulation)
```

To create a store, you call the `create` function, which takes an initializer callback function. The callback function returns an object containing the properties and methods of the state. 

The initializer function has a `set` parameter, which itself is a function that merges state. Note: you can either pass the new state directly as an object, or you can pass a callback containing a `state` parameter that you can return into the `set()` function. 

Below is a typical set state boilerplate code for input fields:
```javascript
const useStore = create((set) => ({
  firstName: '',
  lastName: '',
  updateFirstName: (firstName) => set(() => ({ firstName: firstName })),
  updateLastName: (lastName) => set(() => ({ lastName: lastName })),
}))

function App() {
  // "select" the needed state and actions, in this case, the firstName value
  // and the action updateFirstName
  const [firstName, updateFirstName] = useStore(
    (state) => [state.firstName, state.updateFirstName],
    shallow
  )

  return (
    <main>
      <label>
        First name
        <input
          // Update the "firstName" state
          onChange={(e) => updateFirstName(e.currentTarget.value)}
          value={firstName}
        />
      </label>

```
**Changing Nested States**

The `set` function only merges state at one level. If you have a nested state:
`topics: {exercises : 0}` for instance, 
you'll need the spread operator to update nested state:
```javascript
const useTopicStore = create((set) => ({
    topics: {exercises: 0}, 
    increaseExercises: () => set((state) => ({
        topics: {...state.topics, exercises: state.topics.exercises + 1}
    }))
}))
```
Basically, you're still returning the topics object, with the new state and a copy of the other states in the object. 

## Understanding Immer
This is where Immer comes in. Instead of doing: 
```javascript
increaseExercises: () => set((state) => ({
        topics: {...state.topics, exercises: state.topics.exercises + 1}
    }))
```
you can do: 

```javascript
import produce from "immer"
increaseExercises: () => set(produce((state) => {++state.topics.exercises}))
```

In this case, instead of returning the entire state object, the `produce` function allows you to directly 'dive' into the object and edit the desired fields. It'll produce a 'draft' object that contains the new edits, which you'll then set the state to. 

Let's see what exact is Immer. Immer is basically a `produce` function, that has the effect of allowing you to edit the 'inner nested properties' directly without using spread syntax. 

Take, for instance, if you want to alter the `done` property of the first item, and add a new item: 

```javascript
const baseState = [
    {
        title: "Learn TypeScript",
        done: true
    },
    {
        title: "Try Immer",
        done: false
    }
]
```

Without Immer, we have to shallow copy every level of the state structure that needs to be changed:
```javascript
const nextState = baseState.slice()
nextState[1] = {
    ...nextState[1], 
    done: true
}

nextState.push({title: "Tweet about it"})
```

With immer, you can call the `produce` function, which will take the state we want to start with, and a 'recipe' function that is passed a draft that we can do straightforward mutations:

```javascript
import produce from "immer"

const nextState = produce(baseState, draft => {
    draft[1].done = true
    draft.push({title: "Tweet about it"})
})
```

Produce outputs the next state. The mental thing to note is that within the callback, you can alter the draft state directly, without needing to do any copying whatsoever. You can target *any* item. Consider this: 

```javascript
function toggleTodo(state, id) {
    return produce(state, draft => {
        const todo = draft.find(todo => todo.id === id)
        todo.done = !todo.done
    })
}
```

In this case, you're identifying the state to alter with a `find()` function, and then altering the state directly. It's very slick. 

**Curried producers**
If you create a curried producer function, it becauses very intuitive to use a function to alter a specific state. The following `toggleToDo()` function targets the state to toggle by ID. 

```javascript
const toggleTodo = produce((draft, id) => {
    const todo = draft.find(todo => todo.id === id)
    todo.done = !todo.done
})

const baseState = [
    {
        id: "JavaScript",
        title: "Learn TypeScript",
        done: true
    },
    {
        id: "Immer",
        title: "Try Immer",
        done: false
    }
]

const nextState = toggleTodo(baseState, "Immer")
```

## How to use them together 
The immer documentation shows how you can very quickly alter nested states using immer and react. This is the [code example](https://codesandbox.io/s/immer-usestate-ujkgg?file=/src/index.js:684-695).

But to understand this, I'll need to understand the `useCallback` hook. 

**useCallBack**
According to [react documentation](https://beta.reactjs.org/reference/react/useCallback), useCallback is a react hook that lets you cache a function definition between re-renders.

`const cachedFn = useCallback(fn, dependencies)`

```javascript
import { useCallback } from 'react';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
```

In this page, I have a handleSubmit button, which makes a post request to a URL that uses the productId, referrer, and orderDetails props. 

The purpose of this is that React will return the function back to me during the initial render, *not call it*. On subsequent renders, I'll get the same function if dependencies have not changed. IF they have, it'll give me the function that I have passed during the current render, and store for later use. 

So, in the code example for Immer and React: 

```javascript
import React, { useCallback, useState } from "react";
import produce from "immer";

const TodoList = () => {
  const [todos, setTodos] = useState([
    {
      id: "React",
      title: "Learn React",
      done: true
    },
    {
      id: "Immer",
      title: "Try Immer",
      done: false
    }
  ]);

  const handleToggle = useCallback((id) => {
    setTodos(
      produce((draft) => {
        const todo = draft.find((todo) => todo.id === id);
        todo.done = !todo.done;
      })
    );
  }, []);

  const handleAdd = useCallback(() => {
    setTodos(
      produce((draft) => {
        draft.push({
          id: "todo_" + Math.random(),
          title: "A new todo",
          done: false
        });
      })
    );
  }, []);

  return (<div>{*/ See CodeSandbox */}</div>)
}
```
We generally see that the handlers are wrapped up in the useCallback functions. The logic within the handler is straight forward - it calls the `setTodos` dispatch function, with the produce() function, which creates the entire object with the new state. 

The element that triggers the handlers are also fairly straightforward:

` <button onClick={handleAdd}>Add Todo</button>`

So what is new here? Well, literally nothing. All it does is use the produce function to create the new state and then call `setState`. 

Okay at this point, I seem to have an okay understanding of Immer and Zustand. Let me try to debug my app and figure out what exactly is going on. 