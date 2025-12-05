---
title: "Recently Discovered Zustand"
date: 2023-01-08T10:22:44+08:00
# weight: 1
# aliases: ["/first"]
tags: ["zustand", "redux", "state management"]
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
    image: "https://res.cloudinary.com/dl4murstw/image/upload/v1673146925/76pn7d_v9nuit.jpg" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

So whilst wiring up my the frontend CRUD buttons to the backend routes in this project that I am currently working on, I realized I needed to implement a simple flux architecture to manage global state. 

However, I haven't used Redux in a while, and based on my experience, setting up reducers, and their various dispatch statements, was rather tedious, especially for a small project, so I decided to google what alternatives to Redux was there. 

And then I came across Zustand. From the online reviews Zustand seemed to be pretty popular; and when I headed over to its docs, integration seemed fairly simple. 

I poured over the docs on my way to the BBQ with my partner's family, and implemented some of it on the train. 

And by the next morning (i.e. now), I have already implemented 'reducers' and 'dispatch' functionality for the getAll and create new actions. 

Can't believe how intuitive and quick to set up it is. 

Let me briefly take you through the simple implementation I did. 

### Background on my app 
The context is not so important, but all you need to know is that I need to get a global state `topics` from database, which is an array of `topic` items, and I am trying to create a new `topic` by clicking on a button. This means, after hitting the backend 'new topic' route, I'll need to persist the new topic data into the frontend `topic` state. Classic pattern resolvable by redux. 

First, I defined the topicStore in `/store.ts`: 

```javascript {hl_lines=["4-20"]}
import create from 'zustand'
import produce from 'immer'

const useTopicStore = create((set) => ({
    topics: [],
    setTopics: (data: any) => set({ topics: data}),
    addTopic: (topicData: any) => 
      set(
        produce((draft) => {
          draft.topics.push({
            _id: topicData._id,
            userId: topicData.userId,
            name: topicData.name,
            exercises: topicData.exercises
          })
        })
      )
  }))

  export { useTopicStore }

```
As you can see, the store consists of a state, `topics`, set to an empty array, and a `setTopics` function that takes a `data` argument and set it to the `topics` state. I'll get to the `addTopic` function later. 


Next, I go to my app root, `App()` component, and use the states in the store: 

```javascript {hl_lines=["2-3"]}
export default function App({ Component, pageProps }: AppProps) {
  const topicsData = useTopicStore((state) => state.topics)
  const setTopicsData = useTopicStore((state) => state.setTopics)
  ...
  }
```

Then I set the topic state in the store within an effects hook: 

```javascript {hl_lines=["8-11"]}
export default function App({ Component, pageProps }: AppProps) {
  const topicsData = useTopicStore((state) => state.topics)
  const setTopicsData = useTopicStore((state) => state.setTopics)

  const topicsURL: string = 'http://localhost:3000/api/topics'
  

  useEffect( () => {
    axios.get<Topic[]>(topicsURL)
      .then(res => setTopicsData(res.data))
  }, [setTopicsData])


  return (
      <Component {...pageProps} />
  )
}

```
This initializes the global topics state with the data in the db. 

Note, using Zustand, providers are not needed!

To consume the state, simply head to whichever component you want to use it in, and do the following:

``` javascript {hl_lines=[2, 14]}
const AddTopicModal = ( { setIsModalOpen }: {setIsModalOpen: React.Dispatch<React.SetStateAction<boolean>>}): JSX.Element => {
  const addTopicToStore = useTopicStore(state => state.addTopic)
  const [ newTopicName, setNewTopicName ] = useState<string>('')
  
  const handleInputChange = (e: React.FormEvent<HTMLInputElement>) => {
    const value = (e.target as HTMLInputElement).value
    setNewTopicName(value)
    console.log(value)
  }
  
  const handleSubmit = async (e: React.FormEvent<HTMLInputElement>) => {
    e.preventDefault
    const res = await addTopic(newTopicName, "63b15184daae2f5af31faa71")
    addTopicToStore(res)
    setNewTopicName('')
    setIsModalOpen(false)
  }

```

In my case, I needed to add the topic state within the handleSubmit() button - quite a typical pattern when you POST data to the DB. 

And viola - the frontend is rendered beautifully. 

3 steps in total? 

1. Store definition
2. Initialize data
3. Add it to wherever store needs to be updated. 

*A way more intuitive and simple primitive*, especially for light weight use cases. 

:)