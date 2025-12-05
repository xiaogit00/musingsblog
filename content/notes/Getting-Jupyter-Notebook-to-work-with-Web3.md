---
title: "Getting Jupyter Notebook to Work With Web3"
date: 2021-11-02T13:41:04+08:00
# weight: 1
# aliases: ["/first"]
tags: ["web3"]
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
I was trying to set up my python environment for accessing web3 data, and there was an issue when I tried to import web3 from within Jupyter notebook. It keeps telling me web3 module not found.

Apparently, I should only access web3 from a virtual environment. A virtual environment, it turns out, is a separate environment that you can create that simulates the NPM style of isolated project environments. It allows you to create self-contained dependencies. [This](https://towardsdatascience.com/virtual-environments-104c62d48c54) is a great article explaining it.

I followed the following steps to get web3 ready from within jupyter notebook:

1. Create a new project folder and navigate to it.  
2. Create a new virtualenv called venv  
`virtualenv -p python3 venv`
3. Activate new virtual env  
`source venv/bin/activate`  
You're now operating from within virtualenv  
4. Install web3 in virtual env  
`pip install web3`
5. Install ipykernel (following this [article](https://stackoverflow.com/questions/54129100/cannot-import-python-package-in-jupyter))  
`pip install ipykernel`  
6. Install a new kernel called web3Project  
`ipython kernel install --user --name=web3Project`
7. Open jupyter notebook with my shortcut: jn  
Navigate to web3Project kernel on the right. Viola: `import web3` works.
