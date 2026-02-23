---
title: "Observations Training 3 Models on MNISTFashion"
date: 2026-02-22T15:31:14+08:00
math: true
# weight: 1
# aliases: ["/first"]
tags: ["deep learning"]
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
I wanted to see what are the effects of training some simple models on this dataset, namely, a Shallow 1-layer neural network, a deep 3-layer neural network, a variant of the deep neural network with wider dimensions, as well as a simple 1-layer CNN. 

The results of the models are as follows: 

| Model    | Dimensions| Accuracy|
| -------- | ------- | ------- |
| Shallow NN  | 16    | 78.4%    |
| Deep 3-layer NN | 8-8-8     | 79.2%     |
| Deep 3-layer NN| 16-12-8    | 80.6    |
| CNN      |     8     |   82.6%  |

These corresond to models #1-4 on this page: https://models.minimumloss.xyz/

I'll note my main observations below:


### 1. There's no significant difference in accuracy between the three models  
All models reached around 80% accuracy; although the CNN is somewhat better.

### 2. The shallow neural network converged to a high level of accuracy faster than the deeper ones:  
This is the loss chart for the shallow NN:
![Shallow NN errors](https://models.minimumloss.xyz/api/models/1_MNISTFashion_shallowNN_18FEB26/errors-chart)
*Fig 1: Shallow NN*

After 20 epochs, it managed to converge to 79% accuracy. 

Conversely, if we take a look at the loss chart for the deep NN (model 2):

![](https://models.minimumloss.xyz/api/models/2_MNISTFashion_deepNN_21FEB26/errors-chart)
*Fig 2: DeepNN (model 2)*

We see that after 20 epochs, we're still at around 60% accuracy - it would take us 80 epochs (4 times the training resources) than the shallow network to reach the same level of performance of around 80%.

### 3. The deeper networks tend to be a bit more unstable at the start:
Observe fig 2 above: we see that at the start, the accuracy would tend to oscillate. Deeper networks tend to suffer from the cold start problem, where loss would go up and down before finally converging.

### 4. We don't see signs of overfitting on all three:  
In all of the models, we see that test loss and train loss doesn't diverge. Which means that our model is extrapolating well to unseen data. 


### 5. The CNN converges really fast
After the first epoch, the CNN model is already seeing 62% accuracy - which means that it's converging and learning really fast. In fact, it reaches 80% accuracy with just 13 epochs. For the rest of the epochs, it's already making small adjustments and learning more subtle things to increase accuracy. 

![](https://models.minimumloss.xyz/api/models/4_MNISTFashion_SimpleCNN_23FEB26/errors-chart)
*Fig 3: SimpleCNN*

Comparing it to the deep NN, the CNN takes less than 5 epochs to reach >70% accuracy, whereas the deep NN needs 40 epochs!

### 6. For CNN, we see signs of overfitting the longer we train it

Referring to fig 3, towards the 35-40th epoch, we see that there is a divergence between test and train results. Specifically, test errors are constantly decreasing (we're modelling the training data better and better) with no accompanying generalizability to the test data. 