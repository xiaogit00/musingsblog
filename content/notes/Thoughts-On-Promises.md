---
title: "Thoughts on Promises"
date: 2021-12-07T10:15:55+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Promises", "Async-await", "error handling"]
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

I've always found promises rather confusing.

After recapping it for the nth time this morning, I would like to detail here my main takeaways so that I can finally commit them to memory.

## Recap on Promises

Firstly, to understand promises, you first have to understand threading. Javascript is single-threaded, meaning that all code execute line by line, one after another.

When you create a promise, you're essentially creating another thread that is added to the *event queue* and *importantly*, executed after your main code block.

Essentially, this effectively means that they function something like effect hooks in React.

The implication of this is that you cannot access objects returned by promises in the main code block! All references have to be in the *then* statement.

Second, the two intermediate states of the promise are: resolved and rejected. When it is resolved, you can access the response object via the .then() statement. If it's rejected, you can access the error object via the .catch() statement.

## Async-await

Now, when you create an async function, it returns a promise. You can then handle it like how you'd normally handle promises - in `.then()` statements.

It basically says: take this function off the main thread and into async event queue.

```javascript
let hello = async () => {
	return ‘hello’
}

hello().then(res => console.log(res))

//or, shorthand
hello().then(console.log)
```

The method above can be known as **.then approach to promises**.

The other way to access the data of promises is to use the `await` method.

Await allows you to code as if its synchronous by pausing your code on the line where you attach `await` in front of any async promise-based function. The result is returned.

For instance
```javascript
let myFetch = async () => {
	let response = await fetch(‘coffee.jpg’)
	if (!response.ok) {
		throw new Error(`HTTP error! status: ${response.status}`);
	}
	let myBlob = await response.blob()
	let objectURL = URL.createObejctURL(myBlob)
}

myFetch()
.catch(e => {
	console.log(“there’s a problem with fetch: ” + e.message)
})

```

`let response = await fetch(‘coffee.jpg’)` will cause code execution to pause here, until asset is fetched. Then, myBlob will run - and code is paused there as well until myBlob is fetched, afterwhich `objectURL` line executes.

### Different methods of calling Async/await and its relation to time

When you await a promise based async method, the code is paused until the promise is resolved or rejected.

The following code will take 9 seconds:

#### Method 1 - normal await call
```javascript
async function timeTest() {
  await timeoutPromise(3000);
  await timeoutPromise(3000);
  await timeoutPromise(3000);
}

timeTest()

```

However, when you **store the promise methods in variables and call await on the variables**, you essentially create multiple simultaneous threads.

The following code takes 3 seconds:

#### Method 2 - variable call

```javascript
async function timeTest() {
  const timeoutPromise1 = timeoutPromise(3000);
  const timeoutPromise2 = timeoutPromise(3000);
  const timeoutPromise3 = timeoutPromise(3000);

  await timeoutPromise1;
  await timeoutPromise2;
  await timeoutPromise3;
}

```

### When you store the Promise objects in variables, it ‘has the effect of setting off their associated processes all running simultaneously’.

## Error Handling

### Regular promises
There are two ways to handle error for promises. For regular promises, use `.then().catch()`. If promise is rejected, `.catch()` will run.

For example:

```javascript
myFetch().then((blob) => {
  let objectURL = URL.createObjectURL(blob);
  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
})
.catch((e) =>
  console.log(e)
);
```

### Async-await
For async-await, use try-catch to catch errors.

```javascript
async function myFetch() {

	try {
		let response = await fetch(‘coffee.jpg’);

		let image = document.createElement(‘img’)

	} catch(e) {
	console.log(e);
	}
}
```

## Further subtleties on working with Promises

This article (https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html) is a really good resource on the subtleties of working with promises.

Basically, he posits that there are only three things you do inside a `.then()` function.

```javascript
somePromise().then(function () {
  // I'm inside a then() function!
});

```

1. return another promise
2. return a synchronous value (or undefined)
3. throw a synchronous error

### 1. Return another promise

```javascript
getUserByName('nolan').then(function (user) {
  return getUserAccountById(user.id);
}).then(function (userAccount) {
  // I got a user account!
});

```
Here, the `return` is crucial because if not, the `getUserAccountById()` would actually be a side effect, and the next function would receive undefined instead of the userAccount.

### 2. Returning a synchronous value
```javascript
getUserByName('nolan').then(function (user) {
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];    // returning a synchronous value!
  }
  return getUserAccountById(user.id); // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
});
```

Here, the function will return an inMemoryCache of user if it's present. That is a synchronous value. If not, return the promise.

In some ways, `.then()` functions similar to `await`, in that it allows us to control execution flow.

Author says to: *I make it a personal habit to always return or throw from inside a then() function* because non-returning functions in JS technically returns *undefined*.

### 3. Throw a synchronous error

```javascript
getUserByName('nolan').then(function (user) {
  if (user.isLoggedOut()) {
    throw new Error('user logged out!'); // throwing a synchronous error!
  }
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];       // returning a synchronous value!
  }
  return getUserAccountById(user.id);    // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
}).catch(function (err) {
  // Boo, I got an error!
});
```

Our catch() will receive a synchronous error if the user is logged out, and it will receive an asynchronous error if any of the promises are rejected. Again, the function doesn't care whether the error it gets is synchronous or asynchronous.

This is especially useful because it can help identify coding errors during development. 
