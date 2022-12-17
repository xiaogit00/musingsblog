---
title: "Steps for Deploying a React App to Netlify & Changing Domain"
date: 2022-12-17T14:19:23+08:00
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

## Deploying Site to Netlify 

1. Upload project into Github 

2. Login to Netlify

3. Click `Add a New Site` -> import an existing project 

4. Follow instructions 


---

# Changing Domain 
1. Go to Netlify `Site Setting -> Domain Management`

2. Select `Add custom domain` -> select Verify 

3. You need to add DNS records on your provider to point your domain or subdomain to your site on Netlify. -> under `Custom Domains`, click `Check DNS Configuration`

4. It should open up to a DNS configuration page, with a list of name server hostnames:
dns1.p01.nsone.net
dns2.p01.nsone.net
dns3.p01.nsone.net
dns4.p01.nsone.net

5. Go to external DNS host site, cPanel, and change the name server records. 

6. Wait for 24 hours for the changes to propagate. 

The main thing to learn here is that you're changing the Name Server records of your DNS site, to point to Netlify's servers. 