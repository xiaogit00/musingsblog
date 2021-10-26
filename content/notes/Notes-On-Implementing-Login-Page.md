---
title: "Notes on Implementing Login Page"
date: 2021-10-13T08:42:54+08:00
# weight: 1
# aliases: ["/first"]
tags: ["login"]
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

OKay I'll just need to make some quick notes with regards to login features of a website, and this is a rather new and elegant way of doing it.

### Step 1: Decide on where in the component chain you're going to implement login requirements.
In my case, it's App.js, because everything else within the app is supposed to be 'private' and an individual's views of their own portfolio.

### Step 2: Create a login page.
I've used the stock one from material UI, which looks great. First, create a login.js component. Paste the content from [this template](https://github.com/mui-org/material-ui/blob/next/docs/src/pages/getting-started/templates/sign-in/SignIn.js) into the component.

### Step 3: Within App.js, import Login component, and conditionally render it if token is not set.
This will ensure that none of the other components in your App are rendered if the token is not set. E.g. if you go /streams or /trades, it'll auto redirect to Login page.
```javascript {hl_lines=[2, "6-10"]}
import Dashboard from '../Dashboard/Dashboard';
import Login from '../Login/Login';
import Preferences from '../Preferences/Preferences';

function App() {
  const [token, setToken] = useState();

  if(!token) {
    return <Login setToken={setToken} />
  }

  return (
    <div className="wrapper">
      <h1>Application</h1>
      <BrowserRouter>
        <Switch>
        ...
```

### Step 4: Backend API Config
Most likely, you would have configured a backend login API POST route that would receive a username and a password, and return a token. In my case, if I make a POST request to localhost:3003/api/login, I'll receive a token if login is valid. The token corresponds to the userId.

### Step 5: Pass setToken props into Login component, and specify the props types as function

```javascript {hl_lines=[2, 6, "27-29"]}
import React from 'react';
import PropTypes from 'prop-types';

import './Login.css';

export default function Login({ setToken }) {
  return(
    <div className="login-wrapper">
      <h1>Please Log In</h1>
      <form>
        <label>
          <p>Username</p>
          <input type="text" />
        </label>
        <label>
          <p>Password</p>
          <input type="password" />
        </label>
        <div>
          <button type="submit">Submit</button>
        </div>
      </form>
    </div>
  )
}

Login.propTypes = {
  setToken: PropTypes.func.isRequired
}
```

### Step 6: Make the form controlled with state hooks

```javascript {hl_lines=[2,3,10,14]}
export default function Login({ setToken }) {
  const [username, setUserName] = useState();
  const [password, setPassword] = useState();
  return(
    <div className="login-wrapper">
      <h1>Please Log In</h1>
      <form>
        <label>
          <p>Username</p>
          <input type="text" onChange={e => setUserName(e.target.value)}/>
        </label>
        <label>
          <p>Password</p>
          <input type="password" onChange={e => setPassword(e.target.value)}/>
        </label>
```

### Step 7: Make a POST request with the data
In my case, I want the data to be sent to 'localhost:3003/api/login' onSubmit.
So, I will be coding the POST request within the *handleSubmit* button of *login.js*.

```javascript
export default function Login({ setToken }) {
    const handleSubmit = (event) => {
        event.preventDefault()
        const data = new FormData(event.currentTarget)
        // eslint-disable-next-line no-console
        console.log({
            email: data.get('email'),
            password: data.get('password'),
        })
    }
}
```
In my case, the form uses *FormData* to get the data of the form, which I can access with `data.get('email')` etc.

Previously, the way I send data to any api is to set a dispatch(), with an action creator. The action creator will trigger a call to API via axios, and at the same time update the store. In this case, I don't need to update the store - a simple call to API via axios seems to be fine.

Within my login.js, I imported axios, and set the login URL:
```javascript
import axios from 'axios'

const loginURL = 'http://localhost:3003/api/login'
```

The actual call should look something like this:
```javascript
const newLogin = async (loginCredentials) => {
    const response = await axios.post(loginURL, loginCredentials)
    return response.data
}
```

And then I will simply call `newLogin(loginCredentials)`, where `loginCredentials` is defined in event handler. The entire event handler now looks like this:
```javascript
const handleSubmit = (event) => {
    event.preventDefault()
    const data = new FormData(event.currentTarget)
    // eslint-disable-next-line no-console
    const loginCredentials = {
        email: data.get('email'),
        password: data.get('password'),
    }
    const newLogin = async (loginCredentials) => {
        const response = await axios.post(loginURL, loginCredentials)
        return response.data
    }
    const {token} = newLogin(loginCredentials)

    setToken(token)

}
```
Hopefully at this point, I would have access to the variable `token`, which stores the token of my login.

Finally, this is the crucial part. I call `setToken(token)`. Recall that the login component has a prop called setToken, which is a function defined in App.js. As of yet, we have not defined setToken, but it should look something like this eventually.

```javascript
function setToken(userToken) {
  localStorage.setItem('token', JSON.stringify(userToken));
}
...

function App() {
  const token = getToken();

  if(!token) {
    return <Login setToken={setToken} />
  }
}

```

Notice that we conditionally return a Login component with the setToken as a callback prop, which, upon fulfilment with a token, sets the sessionStorage as the userToken.

### Step 8: Implementing sessionStorage

In short, sessionStorage allows us to access the token even after a refresh of the page but not when we open a new tab or window; localstorage allows us to access the token even after we close the window, until we set a designated token expiry limit.

Implement SessionStorage by defining two functions (getToken and setToken) in *app.js*, calling one of them `getToken()` immediately upon entering component body, and the other one sent as props to *Login* component.


App.js:  
```javascript
import Login from '../Login/Login';
import Preferences from '../Preferences/Preferences';

function setToken(userToken) {
  sessionStorage.setItem('token', JSON.stringify(userToken));
}

function getToken() {
  const tokenString = sessionStorage.getItem('token');
  const userToken = JSON.parse(tokenString);
  return userToken?.token
}

function App() {
  const token = getToken();

  if(!token) {
    return <Login setToken={setToken} />
  }

  return (
    <div className="wrapper">
      ...
    </div>
  );
}

export default App;
```
setToken is passed into Login component as callback prop (a kind of functional prop like handleClick that is only fulfilled upon receiving data in the children component), whereas `getToken`, below:

```javascript
function getToken() {
  const tokenString = sessionStorage.getItem('token')
  const userToken = JSON.parse(tokenString)
  return userToken?.token
}

```
> Short sequence of events: When App component is rendered, it calls getToken(). If it is stored in sessionStorage, a token will be returned. If not, it will be empty. If token is not set, we render Login page. After we key in data, the Login page will send an Axios request to login API, and upon submit, will invoke the setToken prop, which will invoke a call within App.js, and set the sessionStorage as token value. Then, on App component render again, getToken() will call and set the token, and the rest of the components will render with the supplied token.

Sidenote: The optional chaining operator (?) is used because on first access, the token is undefined and when you access the property, you would generate an error. This is a more elegant solution to 'getting past the first render problem'.

**However, when you click submit, React does not bring you out of the login page**. This is because React doesn't have a statehook that causes rerender of the page.


### Step 9: Creating a hook for token to cause rerender.
You'll need to refactor your app to make use of the state hook. Create useToken.js.

*useToken.js:*
```javascript
import { useState } from 'react';

export default function useToken() {
  const getToken = () => {
    const tokenString = sessionStorage.getItem('token');
    const userToken = JSON.parse(tokenString);
    return userToken?.token
  };

  const [token, setToken] = useState(getToken());

  const saveToken = userToken => {
    sessionStorage.setItem('token', JSON.stringify(userToken));
    setToken(userToken.token);
  };

  return {
    setToken: saveToken,
    token
  }
}

```

Within App.js, change it to:  

*app.js:*
```javascript {hl_lines=[4, 8]}
import Dashboard from '../Dashboard/Dashboard';
import Login from '../Login/Login';
import Preferences from '../Preferences/Preferences';
import useToken from './useToken';

function App() {

  const { token, setToken } = useToken();

  if(!token) {
    return <Login setToken={setToken} />
    }
}
```

What's happening is this. useToken() is a function that returns an object with two properties: setToken and token. When the app is first rendered, it creates two variables on line 8 of above. Line 8 does quite a few things.

First, it tries to getToken.
`token = useToken().token` will first trigger `useToken()` which will trigger `useState(getToken())`, which in turn trigers `getToken()` which looks for token within sessionStorage and returns if there is. If not, it returns null. On first render, this will be null. Hence, token is undefined.

Then, it makes available a setToken function, via `useToken().setToken`. When we call `setToken(token)` later via callback within Login prop, it'll set sessionStorage.

Third, login page is rendered if token is not set. Within the page, we make a POST request to Axios which calls `setToken(token)`. `setToken()` is called within App.js, setting the sessionStorage.
