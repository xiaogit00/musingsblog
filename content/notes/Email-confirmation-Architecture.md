---
title: "Email Confirmation Architecture"
date: 2021-12-27T13:51:36+08:00
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

So I've got a fully functioning app and a login system - the next feature I want to add to it is an email confirmation system.

How do I do that?

In this article, I'll be summarizing the main points for implementing such a system based on [this](https://blog.bitsrc.io/email-confirmation-with-react-257e5d9de725) website.

FRONT END

1. App.js containing routes
So, within their App.js, the basic logic is this: if the path points to /, then we go to login landing.

If it points to confirm/:id, it'll bring us to the confirm component. ID here refers to the unique id the database creates and is available on 'this.props' inside the <Confirm /> component at this.props.match.params.id

2. Landing
Landing is the user login page. The main job of this page is to submit the email as post request to the backend server.

3. Confirm
Confirm.js is the component loaded by React Router when user clicked on the unique link sent to the email.

The link in the email will look something like this: http://localhost:3000/confirm/5c40d7607d259400989a9d42

This is basically a request sent to express server, where :id is the unique id created by Mongo.
THen, we send a get request to: /email/confirm/${id}, and display the response data.

4. Adding a spinner button that's v useful

BACKEND
1. Create a new gmail account
2. Add the gmail credentials into the server's .env file.
3. Have a user model ready - with a 'confirmed' field

const fields = {
  email: {
    type: String
  },
  confirmed: {
    type: Boolean,
    default: false
  }
}

4. In your App.js, add these two endpoints:

app.post('/email', emailController.collectEmail)

app.get('/email/confirm/:id', emailController.confirmEmail)

5. Email Controller:
.collectEmail()
when email is sent to this /email endpoint, Email controller finds the user by email. if User is not found, create user, and send Email to user, using the sendEmail() service. Else, if user is found and not confirmed, resend email.

Else, if user found, and email confirmed, then send message: already confirmed.

.confirmEmail
This function is for when the user clicks on the confirmation link in their email.
First, you find the user by ID. If user found and user.confirmed is not true, then update to user.confirmed.

Else, send already confirmed.

6. Create a email.msgs.js to send messages.

7. The sendEmail() service is created using email.send.js

8. Email Templates - creates the templates for the email.

That's about all of the concepts for this. I'll now need to figure out how to implement this in this specific instance of my site.

---
