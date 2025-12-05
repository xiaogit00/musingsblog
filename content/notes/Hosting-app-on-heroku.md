---
title: "Hosting App on Heroku"
date: 2023-06-28T16:44:16+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Backend", "heroku"]
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
Whilst trying to get my app live onto Heroku, I realized that there were a lot of kinks that I needed to get right. This post attempts to document the complete series of steps on getting both the frontend built statically and copied into the backend, as well as getting the backend hosted on heroku. 

## Steps on hosting both frontend and backend of your app on Heroku

### Step 1: Ensure cors is installed in backend
`npm install cors`

### Step 2: Add cors to *index.js*
`const cors = require('cors')`
`app.use(cors())`

### Step 3: Add Procfile to backend
Create a file called `Procfile` with the following content:
`web: npm start`
And add it to the root of the backend repo. 

### Step 4: Change the PORT variable to:
`process.env.PORT || 3001`

### Step 5: Make sure that you have heroku CLI installed on your laptop
If not, run `brew tap heroku/brew && brew install heroku`

### Step 6: Create Heroku app
`heroku create`

### Step 7: Push to Heroku
`git push heroku main`
If cannot, it might be because your code is not main branch. If it's called master, do: `git push heroku HEAD:master`

### Step 8: Go to Heroku console and change the Config Vars
Under settings, copy your .env variables into there. 

### Step 9: Check that site's live
You should be able to access the api routes via the link generated. 

### Step 10: import *static* plugin in *app.js*

If you are not using React router, you can do something like:
`app.use(express.state('build'))` at the top of the middleware declaration. 

**For this case, I am using React Router in the frontend, which requires this particular set up:**
Add this to the start of the middlewares:
`app.use(express.static(path.join(__dirname, './build')));`

And add this to the end of all the routes, right before unknownendpoint handler:  
```javascript
app.get("/*", (req, res) => {
    res.sendFile(path.join(__dirname, "./build", "index.html"), err => {
        if (err) {
            console.log(err);
        }
    });
});

```
Check out here[^1] for more reasoning. 

## Setting up frontend

### Step 11: On the frontend, change all the calls to `localhost:3001` to relative path: 
`/api/notes` for instance

### Step 12: Add a proxy on the frontend
Within package.json, add: 
`"proxy": "http://localhost:3001"`
This will ensure that the request is redirected to 3001 if the resource is not found. 

### Step 13: Add the following scripts to package.json of Backend:

```javascript
    "build:ui": "rm -rf build && cd ../kotakit-frontend/ && npm run build && cp -r build ../kotakit-backend",
    "deploy": "git push heroku main",
    "deploy:full": "npm run build:ui && git add . && git commit -m uibuild && git push && npm run deploy",    
    "logs:prod": "heroku logs --tail"
```
This will ensure that you can just type `npm run deploy:full` to create a **build** folder on frontend and copy that into backend. 

### (Either do this) Step 14: Run `npm run deploy:full` 
This should ensure that latest build is deployed into Heroku. Your app is now hosted and live. 

### (OR) Step 14: Check that latest frontend build works via backend
`npm run build:ui` -> this should build latest frontend and copy onto backend. 
Run `npm start` in backend - it should be able to host your frontend. 

The main problems I had this time was getting the routes right. 

[^1]: According to [this answer](https://stackoverflow.com/questions/51227859/react-router-doesnt-work-on-express-server), the reason is because React Router does all the routing in the browser, so you should be sending the `index.html` file to your users for every route. Thus that route sends the `index.html` file to every route that's not matched by the routes above. Thus it's important to add the catchall file sender *below* all the API routes, which was what worked in my case. 