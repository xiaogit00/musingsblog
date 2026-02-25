---
title: "Sanity Checking the Results of My Cnn Stock Price Predictor"
date: 2026-02-24T22:39:36+08:00
math: true
# weight: 1
# aliases: ["/first"]
tags: ["deep learning", "cnn"]
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
For my assignment, I was training a 1D CNN model to predict stock prices. Whilst most of the models gave 0.5% accuracy, there was one version of the model trained with a learning rate of 1e-3 that was giving me 80% accuracy. 

I am a little skeptical. It feels a little too good to be true. 

A model that gave 0.8% accuracy would mean that this model would be *extremely* profitable. Sounds a litle too good to be true? 

But how do I begin to verify that? The model implementation does look correct.

---

So I've just rebuilt the model, checking line by line, and it does seem like the results are correct. 

![](https://models.minimumloss.xyz/api/models/5_SPY_CNN_24FEB26/errors-chart)

The implications of this is a little wild. It essentially entails that my model would predict the direction of the price movement correctly 80% of the time, given previous 60 days data. 

What?! Isn't that a crazy hit rate? 

Let's examine a little closer what exactly this model is. Here is the [model definition](https://models.minimumloss.xyz/5_SPY_CNN_24FEB26/definition), and here's the [full training code](https://models.minimumloss.xyz/5_SPY_CNN_24FEB26/training-code). 

---
First, I want to print some eval statements to see what exactly the model is doing. 

So this is the overall overall model definition:

```
StockCNN(
  (layer1): Sequential(
    (0): Conv1d(5, 16, kernel_size=(5,), stride=(1,), padding=(1,))
    (1): BatchNorm1d(16, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
    (2): SiLU()
    (3): MaxPool1d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
  )
  (layer2): Sequential(
    (0): Conv1d(16, 8, kernel_size=(5,), stride=(1,), padding=(1,))
    (1): BatchNorm1d(8, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
    (2): SiLU()
    (3): MaxPool1d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
  )
  (flatten): Flatten(start_dim=1, end_dim=-1)
  (fc): Sequential(
    (0): Linear(in_features=104, out_features=20, bias=True)
    (1): SiLU()
    (2): Dropout(p=0.5, inplace=False)
    (3): Linear(in_features=20, out_features=1, bias=True)
    (4): Sigmoid()
  )
)
```
Printing the weights of the first layer, I see: 

```python
>>> model_sched.layer1[0].weight.shape
torch.Size([16, 5, 5])
```
This is the weights of the first conv layer - 16 output channels, kernel size of 5, and having 5 input channels. So how do I visualize the weights actually? I think it's just 5*5 weights, but 16 of them. Each column corresponds to the multiplication with the corresponding 5 input channels. 

So these are the sets of weights that are trained on the first conv1d layer. 

The shape of the second conv1d layer weights is: 

```python
>>> model_sched.layer2[0].weight.shape
torch.Size([8, 16, 5])
```

Interesting! Of course, there are also linear layer weights: 

```python
>>> model_sched.fc[0].weight.shape
torch.Size([20, 104])
```

Alright, enough inspection on those weights. At this point, I think it's a good idea to try to log some of these things during training so that I have an idea of all the transformations needed for this model to work. 

---

Okay I've logged the model weights transformation between the first and the last, but I am not sure what I am supposed to check out lol they're a wall of numbers, and yes, they are different: 

[Eval results](https://models.minimumloss.xyz/5_SPY_CNN_24FEB26/eval-results)

I suppose the best way to actually see how good these are at predicting prices is to test it live. 

Assuming that I have last 60 day data. Would this model tell me what the direction of tomorrow's data based on SNP would be? If this model is indeed generalizable to unseen data, then theoretically it should. 

Perhaps I should use this as a test for another time. For now, I'll just submit this assignment lol. 