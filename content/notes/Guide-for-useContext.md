---
title: "Guide for UseContext"
date: 2022-12-30T14:42:37+08:00
# weight: 1
# aliases: ["/first"]
tags: ["react", "useContext"]
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
There's basically 3 main things you need do to set up a shared state via useContext. 

1. Determine the top level component you want to pass your context to. For my "Smarter Way to Learn X" case, it was the App component, because any other route beneath it would consume that state. 

2. Create the context in its own file, and export it. This involves a simple 2 line code, in a 'context' folder of your app:

``` javascript
import { createContext } from 'react'

export const TopicsContext = createContext(null)
```

3. "Seed" the context around your parent component: 

``` javascript
import { TopicsContext } from '../contexts/TopicsContext'

export default function App({ Component, pageProps }: AppProps) {
  const [topicsData, setTopicsData] = useState(null)

  const topicsURL = 'http://localhost:3001/topics'

  useEffect( () => {
    axios.get(topicsURL)
      .then(res => setTopicsData(res.data))
  }, [])


  return (
    <TopicsContext.Provider value={topicsData} >
      <Component {...pageProps} />
    </TopicsContext.Provider>
    
  )
}
```
In my case, I needed to fetch the data that will be the shared state first. The fetching is done at the top-most level. 

4. Finally, you can consume the context, wherever you need to!

``` javascript
//TopicName.tsx page 
import { TopicsContext } from '../../contexts/TopicsContext'

const TopicName = () => {
    const topicsData = useContext(TopicsContext)
    //topicsData will now be available.

```

Beautiful solution. 