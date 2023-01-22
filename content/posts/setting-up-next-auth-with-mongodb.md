---
title: "Setting Up Next Auth With Mongodb"
date: 2023-01-22T08:43:04+08:00
# weight: 1
# aliases: ["/first"]
tags: ["nextjs", "auth", "next-auth", "mongodb"]
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
    image: "https://res.cloudinary.com/dl4murstw/image/upload/v1674375609/social-media-card_jupw9n.png" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
So, recently I was exploring options for quickly spinning up an authentication system for a NextJs environment, where I stumbled upon next-auth. 

Spinning it up 'quickly' is important, because I intend to use this framework mostly for the MVP stages of a project, and I don't want to spend too much time configuring it. 

I have to admit, understanding it at first was not so easy, because authentication necessarily brings along with it a lot of different parts. Luckily, resources like [these](https://dev.to/dawnind/authentication-with-credentials-using-next-auth-and-mongodb-part-1-m38) and [these](https://www.youtube.com/watch?v=EFucgPdjeNg&ab_channel=FullStackNiraj) helped. 

In this how-to guide, I am going to take you through how to set up this auth system, assuming that you already have a somewhat ready Next app and is ready to add some authentication to it. 

### Installation and initial Set up

#### Step 1: 
Run `npm install next-auth @next-auth/mongodb-adapter mongodb`

#### Step 2: 
Create the route file in the following directory: `pages/api/auth/[...nextauth].js`. 

### Defining the providers you want to use

#### Step 3: 
Add the following code to the `[...nextauth].js` file
``` javascript {hl_lines=[7, 11]}
import NextAuth, { NextAuthOptions } from "next-auth"
import GoogleProvider from "next-auth/providers/google"
import EmailProvider from "next-auth/providers/email"

export const authOptions: NextAuthOptions = {
  providers: [
    EmailProvider({
      server: process.env.EMAIL_SERVER,
      from: process.env.EMAIL_FROM,
    }),
    GoogleProvider({
      clientId: process.env.GOOGLE_ID,
      clientSecret: process.env.GOOGLE_SECRET,
    })
  ],
  theme: {
    colorScheme: "light",
  },
  callbacks: {
    async jwt({ token }) {
      token.userRole = "admin"
      return token
    },
  },
}

export default NextAuth(authOptions)

```
This will allow us to use the [EmailProvider](https://next-auth.js.org/providers/email) and [GoogleProviders](https://next-auth.js.org/providers/google) to authenticate. Providers[^1] are essentially services that we use to sign in. When we add the providers in this file, they will also automatically be rendered on the frontend in the `api/auth/signin` page via the NextAuth.js [REST API](https://next-auth.js.org/getting-started/rest-api), like so:

![signIn](https://res.cloudinary.com/dl4murstw/image/upload/v1674350575/Xnapper-2023-01-22-09.22.13_iwsl5h.png)

#### Step 4: 
Go to your app root `_app.tsx` and add `<SessionProvider>`. 

```jsx {hl_lines=[13, 15]}
import { SessionProvider } from "next-auth/react"
import "./styles.css"

import type { AppProps } from "next/app"
import type { Session } from "next-auth"


export default function App({
  Component,
  pageProps: { session, ...pageProps },
}: AppProps<{ session: Session }>) {
  return (
    <SessionProvider session={session}>
      <Component {...pageProps} />
    </SessionProvider>
  )
}
```
This will allow us to use the `useSession()` hook throughout the app. This hook is important for checking for user login state against session storage, at various places within the app. 

### Creating a login button
#### Step 5: 
Create a sign in button component, at wherever you want to display it: 

``` jsx
import { useSession, signIn, signOut } from "next-auth/react"

const LoginButton = (): JSX.Element => {
  const { data: session } = useSession()
  if (session) {
    return (
      <>
        Signed in as {session.user.email}
        <button onClick={() => signOut()}>Sign out</button>
      </>
    )
  }
  return (
    <div className="gap-0">
      <p>Not signed in </p>
      <button onClick={() => signIn()}>Sign in</button>
    </div>
  )
}

export default LoginButton
```
The above is a simple architecture that will use the `useSession()` hook to check for the presence of values in the `data` field of the returned object. `session` will be assigned to the value of the `data` field, which is of type `Session`. 

For reference, here's the definition of `useSession()`, and the `Session` object:

```typescript
interface useSession {
    data: Session | undefined | null,
    status: "loading" | "authenticated", "unautenticated"
}

interface Session {
  user: {
    name: string
    email: string
    image: string
  },
  expires: Date // This is the expiry of the session, not any of the tokens within the session
}
```

Thus, if we want to access the email of the logged user via the returned session data (like above), we use: `session.user.email`. 

#### Step 6:
Render the login button at any place of your choosing within the app. Once you click on it, it'll bring you to the `api/auth/signup` route, which will display the various providers you have. 

### Defining your .env variables
#### Step 7: 
Go to .env, and input the following:

```
EMAIL_SERVER=smtp://yourEmail@gmail.com:yourAppPassword@smtp.gmail.com:587
EMAIL_FROM=yourEmail@gmail.com

MONGODB_URI=mongodb://127.0.0.1:27017/testdb2

```
If you want to use any other providers, you'll need to supply the other information. 

Do note: if you're using gmail as your provider, you will need to provide an **App password**. This means that first, your account needs to have 2FA enabled, and then go to **Security** of your Google account instructions. Create a new App password for your email. If you don't do this, you'll have problems sending emails using email provider. 

(The main idea of email provider is that it'll send a 'magic link' using the admin email above, and the user will be able to click on it to sign in.)

The MONGODB_URI is the one you use for connecting to your DB. Here, I am using my local mongodb version. If you're using Mongo Atlas, you'll need to change the URI accordingly.

### Configuring your Mongodb adaptor

#### Step 8: 
Create a file at `lib/mongodb.ts`

#### Step 9:
Add the following code:
```javascript
import { MongoClient } from 'mongodb'

if (!process.env.MONGODB_URI) {
  throw new Error('Invalid/Missing environment variable: "MONGODB_URI"')
}

const uri = process.env.MONGODB_URI
const options = {}

let client
let clientPromise: Promise<MongoClient>

if (process.env.NODE_ENV === 'development') {
  // In development mode, use a global variable so that the value
  // is preserved across module reloads caused by HMR (Hot Module Replacement).
  if (!global._mongoClientPromise) {
    client = new MongoClient(uri, options)
    global._mongoClientPromise = client.connect()
  }
  clientPromise = global._mongoClientPromise
} else {
  // In production mode, it's best to not use a global variable.
  client = new MongoClient(uri, options)
  clientPromise = client.connect()
}

// Export a module-scoped MongoClient promise. By doing this in a
// separate module, the client can be shared across functions.
export default clientPromise
```

#### Step 10:
Add the Adapter to your Next-auth config object [...nextauth].js
```javascript
import { MongoDBAdapter } from "@next-auth/mongodb-adapter"
import clientPromise from "../../../lib/mongodb"

export const authOptions: NextAuthOptions = {
  providers: [
    EmailProvider({
      server: process.env.EMAIL_SERVER,
      from: process.env.EMAIL_FROM,
    }),
    GoogleProvider({
      clientId: process.env.GOOGLE_ID,
      clientSecret: process.env.GOOGLE_SECRET,
    })
  ],
  theme: {
    colorScheme: "light",
  },
  callbacks: {
    async jwt({ token }) {
      token.userRole = "admin"
      return token
    },
  },
  adapter: MongoDBAdapter(clientPromise),
}

export default NextAuth(authOptions)
```

And that is about it! You should be able to do the following: 
- Clicking on the login button at the place you render it
- It'll take you to the default sign in page, where using the email provider, you'll be able to get a magic link to sign in. 

### (Optional) Setting up Google Sign in 

#### Step 10: 
Within the .env file, you'll need to input your GOOGLE_ID and GOOGLE_SECRET. These are created from the Google Cloud Console API credentials. If you haven't set them up, follow the steps [here](https://blog.openreplay.com/user-authentication-with-google-next-auth/#:~:text=To%20get%20Google%20access%20credentials,%E2%80%9D%2C%20click%20on%20this%20button.). 

This will enable you to sign in via the classic Google Oauth. 

### (Optional) Having your own Signin page
If you want to create sign in pages of your own style, add the following in `[...nextauth].js`
```
...
pages: {
    signIn: '/auth/signin',
    signOut: '/auth/signout',
    error: '/auth/error', // Error code passed in query string as ?error=
    verifyRequest: '/auth/verify-request', // (used for check email message)
    newUser: '/auth/new-user' // New users will be directed here on first sign in (leave the property out if not of interest)
  }
```
Be sure to create the actual `signIn.ts` page in `pages/auth/signIn.ts`. The URI `api/auth/signIn` will direct to this page instead of the default page. 

### Conclusion

You will now be able to spin up a working authentication system for your app, within 30mins. It's quite a bit of things to wrap your head around, but as with all good frameworks, once you get over the initial learning curve, it's incredibly easy to set up and get going. 

The really powerful thing about using Oauth and these providers, is that you'll be able to access three preconfigured tables within your DB: Users, Sessions, Verification_tokens. It really abstracts away the need for you to worry about creating models and storing any of these in your DB, whilst at the same time actually storing and being able to query these data. 

[^1]:  NextAuth has pretty good documentation for the three types of providers, giving us insight into what goes on beneath the hood for all of them. The 3 main types are: [OAuth](https://next-auth.js.org/configuration/providers/oauth), [email](https://next-auth.js.org/configuration/providers/email), and [credentials](https://next-auth.js.org/configuration/providers/credentials) (your run of the mill `username` and `password` sign in).