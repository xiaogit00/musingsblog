---
title: "Basic Markdoc Implementation in React"
date: 2023-06-19T13:05:22+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
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
So I was trying to implement Markdoc into my frontend app, which requires a basic markdown section. 

I've encountered Markdoc before, and was quite keen to try it out. And thus I followed along to their instructions on their [integration guide for React](https://markdoc.dev/docs/examples/react). However, I found the guide pretty cryptic, and was basically a much more advanced configuration than what I needed. All I needed to do was to render some markdown file saved on the frontend onto the page. Did I need to create a Markdoc schema? What even are they? (And why bombard new users with unexplained schema definition files?) Did I need to create an express server in my case? No. So detailed below is my own much leaner implementation of it: 

### Step 1: Install the package
`npm install @markdoc/markdoc`

### Step 2: Create md file
Create your md file in your frontend. In my case, I've got it under `/markdoc/electrical.md`. 

### Step 3: Import packages & file
Within the React component you want to render the MD file, import the following: 

``` javascript
import Markdoc from '@markdoc/markdoc'
import electricalContent from '../markdoc/electrical.md'
```

### Step 4: Import md file
To load the md file in your JS file, you'll need to use an effects hook. I made a mistake of importing `fs` on the frontend, following the tutorial, `const rawText = fs.readFileSync(file, 'utf-8');`, but realized that [you can't access fs on the frontend](https://stackoverflow.com/questions/69778195/how-to-use-fs-module-in-react-project-correctly-now-both-javascript-and-types). So in order to use the plain text, you'd have to import it with an effects hook, as there's [no way to directly import markdown as a string in Javascript](https://stackoverflow.com/questions/65395125/how-to-load-an-md-file-on-build-when-using-create-react-app-and-typescript). 

``` javascript
const [contentMarkDown, setContentMarkDown] = useState(null);

    useEffect(() => {
        fetch(electricalContent)
            .then((response) => response.text())
            .then((text) => {
                ...
            });
    }, []); 
```

### Step 5: Apply markdoc transformation

Within the effect hook, use this: 

``` javascript
    useEffect(() => {
        fetch(electricalContent)
            .then((response) => response.text())
            .then((text) => {
                const ast = Markdoc.parse(text)
                const content = Markdoc.transform(ast, {})
                const markdocRender = Markdoc.renderers.react(content, React)
                setContentMarkDown(markdocRender)
            });
    }, []); 
```

### Step 6: Render your markdocRender
The response from `Markdoc.renderers.react(content, React)` is a React node. You can render it directly in your JSX upon load: 

`{contentMarkDown ? contentMarkDown : null}`

Done!

So as you can see, using this configuration, you don't have to create any server side routes (although I get that for more complex applications where you'd want to store your MD files on the server side and access them through a route, that's useful), you'd be able to directly store the MD files on your frontend React App, and you also don't have to create any custom under-documented Markdoc schemas, allowing you to tap into the natively defined nodes of Markdoc. 
