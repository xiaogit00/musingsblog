---
title: "Steps for Implementing a More Robust Backend Development Architecture (Part 1)"
date: 2021-09-22T05:49:51+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Backend", "Express", "MongoDB","Documentation"]
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

You've arrived at the moment where your project is getting serious and you need to begin coding some serious backend. This guide will get you started.

### Goal of Backend Restructure
Goal of the development project is to get the backend to look like this:
- Backend architecture:
	- Index.js
	- App.js
	- Routers:
		- Streams Router
		- Trades Router
		- User Router
		- Login Router
	- Models
		- Trades Model
		- Streams Model
		- User Model
	- Utils
		- Middleware
		- Logger
		- config.js
	- Tests
		- setting up test environment

## Laying the groundworks

### Step 1: Take stock of current project dependencies and structure
Examine package.json to see what dependencies it currently have. Examine the project folder structure and files to check where is the backend mostly stored. For a simple express server, it is possible that all the routes, middleware, models, are stored in index.js.

This is the state of my project before I decided to add robustness to it.

![](https://res.cloudinary.com/dl4murstw/image/upload/v1632261631/Screenshot_2021-09-22_at_6.00.24_AM_wbleea.png)

My index.js was simply:

```javascript
const express = require('express')
const app = express()
const cors = require('cors')
app.use(cors())

const trades = [
    ...
]

const streams = [
    ...
]

app.get('/', (request, response) => {
  response.send('<h1>Hello World!</h1>')
})

app.get('/api/streams', (request, response) => {
  response.json(streams)
})

app.get('/api/trades', (request, response) => {
  response.json(trades)
})

const PORT = 3001
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})

```

There are three get routes, and the data is stored in variables within the index.js file. There's no ways of altering the data via post.

These are my dependencies in my package.json file:
```json
"dependencies": {
  "cors": "^2.8.5",
  "express": "^4.17.1"
},
"devDependencies": {
  "nodemon": "^2.0.12"
}
```
As you can see, pretty bare bones. Time to beef it up!

### Step 2: Install morgan for dev console logging
1. `npm install morgan`
2. `var morgan = require('morgan')` within index.js (for now)
3. `app.use(morgan('tiny'))`[^1]
[^1]: If you're confused about placement of this line of code, use it like how you would middleware: i.e. before the routes.
4. *(Optional)* If you want to customize your morgan message, you could do it like so:
```javascript
morgan.token('data', (req, res) => JSON.stringify(req.body))
app.use(morgan(':method :url :response-time :data'))
```
This would display the response data: e.g.
`POST /api/persons 6.638 {"name":"Ar22to Hellas","number":"040-123456"}`

### Step 3: Set up Eslint.
1. `npm install eslint --save-dev`
2. Initialize eslint with `node_modules/.bin/eslint --init`
Follow this as reference:
![](https://res.cloudinary.com/dl4murstw/image/upload/v1632262735/52be_qpvhqj.png)
3. Enable command `npm lint`
```json {hl_lines=[5]}
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "lint": "eslint ."
  },
}
```

## Restructuring the project
There are no right ways to move forward with the next step. For this case, I've decided to begin with constructing the models for my app.
### Step 4: Creating models
1. Create a new folder `models`
2. Create your first model. This case, `trade.js`.
3. Coding the model
    - a mongoose model basically consists of:
        - a schema where you define the fields and data types `new mongoose.Schema({})`
        - any validator you want to perform on it (e.g. `userSchema.plugin(uniqueValidator)`)
        - display transformation `userSchema.set('toJSON')`
        - model creation `mongoose.model('User', userSchema)`
        - exporting

*Example of User model:*
```javascript
const mongoose = require('mongoose')
const uniqueValidator = require('mongoose-unique-validator')

const userSchema = new mongoose.Schema({
  username: {
    type: String,
    unique: true
  },
  name: String,
  passwordHash: String,
  notes: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'Note'
    }
  ],
})

userSchema.plugin(uniqueValidator)

userSchema.set('toJSON', {
  transform: (document, returnedObject) => {
    returnedObject.id = returnedObject._id.toString()
    delete returnedObject._id
    delete returnedObject.__v
    // the passwordHash should not be revealed
    delete returnedObject.passwordHash
  }
})


const User = mongoose.model('User', userSchema)

module.exports = User
```

### Step 5: Create logger.js file in utils
/utils/logger.js
```javascript
const info = (...params) => {
    if (process.env.NODE_ENV !== 'test') {
        console.log(...params)
    }

}

const error = (...params) => {
    if (process.env.NODE_ENV !== 'test') {
        console.error(...params)
    }
}

module.exports = {
    info, error
}

```

### Step 6: Creating `app.js`
Distribute the responsibility between `index.js` and `app.js`.
`index.js` should import app.js, and has a simple job of opening a port to listen.

*Index.js*
```javascript
const app = require('./app')
const cors = require('cors')
app.use(cors())

const PORT = 3003
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`)
})
```

App.js contains the main app; i.e. database connection.
*app.js*
```javascript
const config = require('./utils/config')
const logger = require('./utils/logger')
const express = require('express')
const app = express()
const mongoose = require('mongoose')
var morgan = require('morgan')

logger.info('connecting to')

mongoose.connect(config.MONGODB_URI)
    .then(() => {
        logger.info('connected to MongoDB')
    })
    .catch((error) => {
        logger.error('error connecting to MongoDB:', error.message)
    })


morgan.token('data', (req, res) => JSON.stringify(req.body))
app.use(morgan(':method :url :response-time :data'))

module.exports = app

```

### Step 7: Connecting to Database
The connection to database is typically done in App.js.

But before you begin the connection, it's good to set the config variables.

1. `npm install dotenv`
2. Create *.env* file in project root
3. Add the following properties to to .env:
MONGODB_URI=''
PORT=3003
4. Add *.env* to .gitignore.
5. Create new file `utils/config.js`
```javascript
//config.js
require('dotenv').config()

const PORT = process.env.PORT || 3003

const MONGODB_URI = process.env.NODE_ENV === 'test'
    ? process.env.TEST_MONGODB_URI
    : process.env.MONGODB_URI

module.exports = {
    MONGODB_URI,
    PORT
}
```
If *process* is underlined by eslint, go to eslintrc and change env from `'browser': true` to `'node': true`.
6.

The following lines in *App.js* are in charge of connection:
```javascript
const config = require('./utils/config')
const logger = require('./utils/logger')
const mongoose = require('mongoose')

logger.info('connecting to')

mongoose.connect(config.MONGODB_URI)
  .then(() => {
    logger.info('connected to MongoDB')
  })
  .catch((error) => {
    logger.error('error connecting to MongoDB:', error.message)
  })
```

### Step 8: Test that the connection works
`npm run dev`

If it's connected, it should display *connected to MongoDB* on console.

### Step 9: Populating the database with some initial sample data
1. Under utils, create a document *sampleData.js* containing your sample data and export them.

Store your data as a list of collections like so:
```javascript
const tradeData = [
{
  email: "user1@domain.com",
  password: "user1",
  posts: []
},
{
  email: "user2@domain.com",
  password: "user2",
  posts: []
},
]
```
2. Import into app.js  
`const tradesData = require('./utils/sampleData').tradesData`  

3. Import models into app.js  
`const Trade = require('./models/trade.js')`  
`const Stream = require('./models/stream.js')`

4. Drop collections in case they already exist:
```javascript
mongoose.connection.dropCollection('trades', (err) => {
    if (err) {
        if (err.code === 26)
            console.log("-- trades collection does not exist")
        else throw err;
    } else {
        console.log('-- trades collection dropped')
    }
})

mongoose.connection.dropCollection('streams', (err) => {
    if (err) {
        if (err.code === 26)
            console.log("-- streams collection does not exist")
        else throw err;
    } else {
        console.log('-- streams collection dropped')
    }
})
```


5. To add them to mongo database,
*Within app.js:*
```javascript
Trade.create(tradesData, (err, trades) => {
    if (err) throw err
    console.log(trades + '\n-- trades inserted successfully')
})

Stream.create(streamsData, (err, streams) => {
    if (err) throw err
    console.log(streams + '\n-- streams inserted successfully')
})

```

6. **However**, in reality, there was a complication in terms of ID management. The gist of it is that, my Streams collection needs to reference the Trade _id field, which are not created yet.

My (inelegant) workaround:
1. Bulk create trades data like above by running `npm run dev`
2. Stop app - to prevent constant writing into DB.
3. Copy _id manually into streams sample data
4. Comment out create trades lines; Run bulk create streams.

It works.The full app.js looks like this - comment out as per necessary. These are just one off executions for development purposes, so it's fine.

```javascript
const config = require('./utils/config')
const express = require('express')
const tradesData = require('./utils/sampleData').tradesData
const streamsData = require('./utils/sampleData').streamsData
const Trade = require('./models/trade.js')
const Stream = require('./models/stream.js')
const app = express()
const logger = require('./utils/logger')
const mongoose = require('mongoose')
var morgan = require('morgan')

console.log(tradesData)
logger.info('connecting to')

mongoose.connect(config.MONGODB_URI)
    .then(() => {
        logger.info('connected to MongoDB')
    })
    .catch((error) => {
        logger.error('error connecting to MongoDB:', error.message)
    })

morgan.token('data', (req, res) => JSON.stringify(req.body))
app.use(morgan(':method :url :response-time :data'))

//**********************************************************
//*                     Clearing collection
//**********************************************************
mongoose.connection.dropCollection('trades', (err) => {
    if (err) {
        if (err.code === 26)
            console.log('-- trades collection does not exist')
        else throw err
    } else {
        console.log('-- trades collection dropped')
    }
})

mongoose.connection.dropCollection('streams', (err) => {
    if (err) {
        if (err.code === 26)
            console.log('-- streams collection does not exist')
        else throw err
    } else {
        console.log('-- streams collection dropped')
    }
})

//**********************************************************
//*                     Populating trades
//**********************************************************
Stream.create(streamsData, (err, streams) => {
    if (err) throw err
    console.log(streams + '\n--streams inserted successfully')
})
Trade.create(tradesData, (err, trades) => {
    if (err) throw err
    console.log(trades + '\n-- trades inserted successfully')
})

module.exports = app

```

That's it for this part, it's getting bit long. Onwards to Part B, where I discuss what else I do after that!
