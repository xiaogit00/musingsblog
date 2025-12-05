---
title: "A Really Pesky Bug in React"
date: 2021-08-25T19:38:27+08:00
# weight: 1
# aliases: ["/first"]
tags: ["React", "common bugs", "effect Hooks"]
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
I am going to document this bug because I believe it's happened before, and it really took me quite a while the previous time to debug also.

I'll call this bug: *can't get past the first render*.

It involves inconsistencies in accessing props(i.e. data) from child components, causing the page to break. Yet, the description is really not helpful: *TypeError: rowData is undefined react*.

The root cause of the bug lies in how React handles the logical flow of ReactHooks. For future me reading this, I'll briefly detail the component structure:

- StreamsTable
  - has a child **TableRow**, which is passed stream={streams}
    - has a child **TableRowItems**, which is passed rowData={rowData}

rowData is dependent on {streams} to be defined. Data is pulled via a useEffect() hook within StreamsTable.

#### The Problem:
Sometimes, the application works, but other times, it completely breaks. The error I receive is *TypeError: rowData is undefined react* - which is created from the most inner most child.

#### The diagnosis:
The problem is caused by the sequence with which useEffect() hook is done within StreamsTable. Upon StreamsTable page render, it **will render the JSX first** before the useEffect() hook is called. This is found by console.logging and seeing that streams, trades, (both of which are passed into TableRow child component) are both undefined. Now, usually that isn't critical. However, when it reaches the inner most child, it means that rowData is not defined. **When you use an effectHook, the first render, including the entire chain of child components, must be successful**. This cannot happen when you're passing props into child components that depends on data to be successfully pulled and defined in parent components.  

#### The Solution:
I added a conditional render within top most parent (StreamsTable) such that if {streams} is not defined, then simply do not show the component (TableRow) that depends on Streams.
```javascript
//within StreamsTable
if (streams.length > 0) {
  return (
    <div className="streams-table-container">
      <TableHead/>
      <TableRow stream={streams[0]} trades={trades}/>

    </div>
  )
} else {
  return (
    <div className="streams-table-container">
      <TableHead/>

    </div>
  )
}
```


Generally, to resolve Can't Get Past the First Render kind of bugs, use a conditional logic in parent component to exclude rendering of problematic components. However, more experienced coders might include safety nets to catch this kind of error, so that the error message would be more helpful.

As a rule, always include the following logging statement within effect hooks:
`console.log("Finished Rendering the first time.")`, like so:

```javascript
useEffect(() => {
  console.log("Successfully finished first render of App and entered effectHook again.")
  axios
    .get('http://localhost:3001/api/trades')
    .then(response => {

      setTrades(response.data)
    })
}, [])
```
This way, when things break, you can see that this line is missing from console.log and realize that it's a Can't get past the first render kind of bug. 
