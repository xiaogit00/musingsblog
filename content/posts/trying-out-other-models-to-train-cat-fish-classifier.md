---
title: "Lessons from using Alexnet to train a cat/fish classifier"
date: 2025-12-09T17:17:28+08:00
# weight: 1
# aliases: ["/first"]
tags: ["CNN", "Alexnet"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://canonical.url/to/page"
# disableHLJS: true # to disable highlightjs
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

### Learnings: 
Using Alexnet model with kernel settings of size=11, stride=4, padding=2, and image size of 227x227, I could reach 70% accuracy with training of 30 epochs. This, unfortunately, is the same level of accuracy as my Simplenet in the [previous post](https://blog.minimumloss.xyz/posts/basic-training-loop-with-pytorch/)

---

### Training
Using a simple neural network from the previous post, I could reach an accuracy level of 0.7. However whilst implementing the following CNN from chapter 3 of the Pytorch book, I realized that I had quite a few issues trying to get it to converge. 


{{< collapse summary="The CNN model from the chapter (AlexNet Original)" >}}
```python
class CNNNet(nn.Module):
    def __init__(self, num_classes=2):
        super(CNNNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 6 * 6, 4096),
            nn.ReLU(),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(),
            nn.Linear(4096, num_classes)
        )

    def forward(self, x):
        x = self.features(x)
        x = self.avgpool(x)
        x = torch.flatten(x, 1)
        x = self.classifier(x)
        return x

```
{{< /collapse >}}


So first, this is the first implementation of my code, which got me an accuracy of 0.7 after 20 epochs:

{{< collapse summary="Version 1 Results" >}}
```
Epoch: 0, Training Loss: 3007.42, Validation Loss: 0.69, accuracy = 0.41
Epoch: 1, Training Loss: 5.41, Validation Loss: 0.69, accuracy = 0.59
Epoch: 2, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 3, Training Loss: 0.69, Validation Loss: 0.70, accuracy = 0.41
Epoch: 4, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 5, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 6, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 7, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 8, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 9, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 10, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 11, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.60
Epoch: 12, Training Loss: 0.69, Validation Loss: 0.67, accuracy = 0.64
Epoch: 13, Training Loss: 0.69, Validation Loss: 0.66, accuracy = 0.64
Epoch: 14, Training Loss: 0.68, Validation Loss: 0.65, accuracy = 0.65
Epoch: 15, Training Loss: 0.67, Validation Loss: 0.63, accuracy = 0.66
Epoch: 16, Training Loss: 0.68, Validation Loss: 0.61, accuracy = 0.69
Epoch: 17, Training Loss: 0.66, Validation Loss: 0.60, accuracy = 0.67
Epoch: 18, Training Loss: 0.66, Validation Loss: 0.59, accuracy = 0.68
Epoch: 19, Training Loss: 0.65, Validation Loss: 0.59, accuracy = 0.70
```
{{< /collapse >}}

{{< collapse summary="Version 1 Training Code" >}}
```python
# %%
# import logging

# logging.basicConfig(
#     filename='traininglog.log',
#     level=logging.INFO, # Log all messages INFO severity or higher
#     format='%(asctime)s - %(levelname)s - %(message)s',
#     filemode='w' # 'a' appends to the file (default), 'w' overwrites
# )

# # Get a logger instance (using the root logger here)
# logger = logging.getLogger()

# %%
import torch.nn as nn
import torch
import torch.nn.functional as F

class CNNNet(nn.Module):
    def __init__(self, num_classes=2):
        super(CNNNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=3, stride=2),
            )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
        nn.Dropout(),
        nn.Linear(256 * 6 * 6, 4096),
        nn.ReLU(),
        nn.Dropout(),
        nn.Linear(4096, 4096),
        nn.ReLU(),
        nn.Linear(4096, num_classes)
        )

    def forward(self, x):
        # logger.info(f"Entered forward of alexnet, x value: \n {x}\n")
        x = self.features(x)
        # logger.info(f"After CNN layer, x value: \n {x} \n")
        x = self.avgpool(x)
        # logger.info(f"After avgpool, x value: \n {x} \n")
        x = torch.flatten(x, 1)
        # logger.info(f"After flatten, x value: \n {x} \n")
        x = self.classifier(x)
        # logger.info(f"After classifier layer, x value: \n {x} \n")
        return x

# %%


# %%
import torchvision
from torchvision import transforms

train_data_path = './data/fish_cat_images/train'

transforms = transforms.Compose([
    transforms.Lambda(lambda img: img.convert("RGB")),
    transforms.Resize((227, 227)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225])
])
train_data = torchvision.datasets.ImageFolder(root=train_data_path, transform=transforms)

# %%
train_data[0][0].shape

# %%
conv1 = nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2)

# %%
conv1(train_data[0][0]).shape

# %%
val_data_path = "./data/fish_cat_images/val/"
val_data = torchvision.datasets.ImageFolder(root=val_data_path, transform=transforms)

test_data_path = "./data/fish_cat_images/test/"
test_data = torchvision.datasets.ImageFolder(root=test_data_path, transform=transforms)



# %%
from torch.utils.data import Dataset, DataLoader

batch_size=64
train_data_loader = DataLoader(train_data, batch_size=batch_size)
val_data_loader = DataLoader(val_data, batch_size=batch_size)
test_data_loader = DataLoader(test_data, batch_size=batch_size)


# %%
import torch.optim as optim



def train(model, optimizer, loss_fn, train_loader, val_loader, epochs=20, device="cpu"):
    for epoch in range(epochs):
        training_loss = 0.0 # each epoch has their own training loss.. 
        valid_loss = 0.0 # ..and validation loss
        model.train() # set it to training  mode
        for batch in train_loader: # to find how many batches, train_data_loader.__len__()
            optimizer.zero_grad() # what this does beneath the hood is that it loops through all the params of model and sets them to grad 0
            inputs, targets = batch # inputs is matrix of (B, C, W, H), targets (B, 1)
            inputs = inputs.to(device)
            targets = targets.to(device)
            output = model(inputs)
            loss = loss_fn(output, targets)
            loss.backward()
            optimizer.step()
            training_loss += loss.data.item() * inputs.size(0) # loss for this batch * batch_size
            
        # by this step, training_loss is the total loss of entire epoch
        training_loss /= len(train_loader.dataset) # divided by the num data points to find unit loss 
        
        model.eval()
        num_correct = 0 
        num_examples = 0
        for batch in val_loader:
            inputs, targets = batch
            inputs = inputs.to(device)
            output = model(inputs)
            targets = targets.to(device)
            loss = loss_fn(output,targets) 
            valid_loss += loss.data.item() * inputs.size(0) #validation loss for this batch
            correct = torch.eq(torch.max(F.softmax(output), dim=1)[1], targets).view(-1) #compare all prediction in batch to ground truth
            num_correct += torch.sum(correct).item() # num correct in batch
            num_examples += correct.shape[0] #examples in batch
        #at this point, valid_loss is total loss of entire epoch
        valid_loss /= len(val_loader.dataset)

        print('Epoch: {}, Training Loss: {:.2f}, Validation Loss: {:.2f}, accuracy = {:.2f}'.format(epoch, training_loss,
        valid_loss, num_correct / num_examples))


# %%
from torchvision.models import alexnet

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
alexnet = CNNNet().to(device)
optimizer = optim.Adam(alexnet.parameters(), lr=0.001)

for inputs, labels in train_data_loader:
    inputs = inputs.to(device)
    labels = labels.to(device)

    outputs = alexnet(inputs)

train(alexnet, optimizer, torch.nn.CrossEntropyLoss(),train_data_loader, test_data_loader, 20, device)



```

{{< /collapse >}}

Some of the features of this model includes: we're using stride 4, kernel 11, and padding 2. This is the original filter specs of Alexnet. I've also resampled the images to aspect ratio of 227x227, in keeping with the kernel dimensions. 

Now it got me thinking, if I extended this to 30 epochs, would the accuracy improve? Let's see. 

Okay, so this is ridiculous. The accuracy fell. What is going on? 

{{< collapse summary="Version 1 - 30 Epochs, results" >}}
```
Epoch: 0, Training Loss: 9667.86, Validation Loss: 0.69, accuracy = 0.59
Epoch: 1, Training Loss: 2.48, Validation Loss: 0.70, accuracy = 0.41
Epoch: 2, Training Loss: 0.70, Validation Loss: 0.89, accuracy = 0.41
Epoch: 3, Training Loss: 0.77, Validation Loss: 0.72, accuracy = 0.41
Epoch: 4, Training Loss: 0.72, Validation Loss: 0.71, accuracy = 0.41
Epoch: 5, Training Loss: 0.71, Validation Loss: 0.71, accuracy = 0.41
Epoch: 6, Training Loss: 0.70, Validation Loss: 0.71, accuracy = 0.41
Epoch: 7, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 8, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 9, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 10, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 11, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 12, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 13, Training Loss: 0.70, Validation Loss: 0.70, accuracy = 0.41
Epoch: 14, Training Loss: 0.69, Validation Loss: 0.70, accuracy = 0.41
Epoch: 15, Training Loss: 0.69, Validation Loss: 0.70, accuracy = 0.41
Epoch: 16, Training Loss: 0.69, Validation Loss: 0.70, accuracy = 0.41
Epoch: 17, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 18, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 19, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 20, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 21, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 22, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.41
Epoch: 23, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.43
Epoch: 24, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.44
Epoch: 25, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.45
Epoch: 26, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.48
Epoch: 27, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.49
Epoch: 28, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.50
Epoch: 29, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.51
```
{{< /collapse >}}

Was the earlier convergence sheer coincidence? Does it have something to do with the batching mechanism? Learning rate? 

I am going to try once more with LR of 0.0001. Okay, it seems like the final loss shot up to 0.72. 

{{< collapse summary="Version 1 | LR=0.0001 | Results" >}}
```
Epoch: 0, Training Loss: 6.21, Validation Loss: 0.69, accuracy = 0.59
Epoch: 1, Training Loss: 0.67, Validation Loss: 0.69, accuracy = 0.59
Epoch: 2, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 3, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 4, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 5, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 6, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 7, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 8, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 9, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 10, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 11, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 12, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 13, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 14, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.59
Epoch: 15, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.59
Epoch: 16, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.68
Epoch: 17, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.70
Epoch: 18, Training Loss: 0.69, Validation Loss: 0.67, accuracy = 0.69
Epoch: 19, Training Loss: 0.68, Validation Loss: 0.65, accuracy = 0.69
Epoch: 20, Training Loss: 0.69, Validation Loss: 0.64, accuracy = 0.65
Epoch: 21, Training Loss: 0.68, Validation Loss: 0.67, accuracy = 0.63
Epoch: 22, Training Loss: 0.67, Validation Loss: 0.65, accuracy = 0.69
Epoch: 23, Training Loss: 0.65, Validation Loss: 0.60, accuracy = 0.69
Epoch: 24, Training Loss: 0.64, Validation Loss: 0.57, accuracy = 0.71
Epoch: 25, Training Loss: 0.64, Validation Loss: 0.57, accuracy = 0.70
Epoch: 26, Training Loss: 0.63, Validation Loss: 0.54, accuracy = 0.72
Epoch: 27, Training Loss: 0.56, Validation Loss: 0.54, accuracy = 0.72
Epoch: 28, Training Loss: 0.60, Validation Loss: 0.52, accuracy = 0.72
Epoch: 29, Training Loss: 0.55, Validation Loss: 0.53, accuracy = 0.72
```
{{< /collapse >}}

What I am currently observing is that with the same training code, some runs converge, whilst others don't. Is this the effects of accuracy? Or learning rate? 

I am going to run one more time, with the exact same spects of Version 1 | LR=0.0001, to see if I can get convergence. 

{{< collapse summary="Version 1 | LR=0.0001 | Run 2 Results" >}}
```
Epoch: 0, Training Loss: 7.67, Validation Loss: 0.69, accuracy = 0.59
Epoch: 1, Training Loss: 0.66, Validation Loss: 0.69, accuracy = 0.59
Epoch: 2, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 3, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 4, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 5, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 6, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 7, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 8, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 9, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 10, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 11, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 12, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 13, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 14, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 15, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 16, Training Loss: 0.69, Validation Loss: 0.69, accuracy = 0.59
Epoch: 17, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.59
Epoch: 18, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.59
Epoch: 19, Training Loss: 0.69, Validation Loss: 0.68, accuracy = 0.59
Epoch: 20, Training Loss: 0.69, Validation Loss: 0.67, accuracy = 0.59
Epoch: 21, Training Loss: 0.69, Validation Loss: 0.67, accuracy = 0.71
Epoch: 22, Training Loss: 0.68, Validation Loss: 0.68, accuracy = 0.71
Epoch: 23, Training Loss: 0.68, Validation Loss: 0.66, accuracy = 0.72
Epoch: 24, Training Loss: 0.68, Validation Loss: 0.63, accuracy = 0.72
Epoch: 25, Training Loss: 0.66, Validation Loss: 0.59, accuracy = 0.71
Epoch: 26, Training Loss: 0.66, Validation Loss: 0.57, accuracy = 0.71
Epoch: 27, Training Loss: 0.63, Validation Loss: 0.55, accuracy = 0.73
Epoch: 28, Training Loss: 0.63, Validation Loss: 0.54, accuracy = 0.73
Epoch: 29, Training Loss: 0.62, Validation Loss: 0.58, accuracy = 0.72
```
{{< /collapse >}}

What we observe in this one is a very different convergence pattern - accuracy stayed flat all the way until Epoch 20, before shooting up to 0.71 drastically. 

And here we see a higher accuracy level of 73%, the greatest that we've seen. 

Okay let's export this model - let's call it 22DEC2025_Alexnet_catfish_classifier_kernel_11_4_2. 

### Using this model to make predictions
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397507/cat1_tikikd.jpg" width="50%"  >}}
Correctly predicts a cat.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397508/cat2_ffedfu.webp" width="50%"  >}}
Correctly predicts a cat.

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397513/cat3_gph8ga.avif" width="50%"  >}}
Wrongly Predicts a fish. 

---

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397516/fish1_mjamuy.webp" width="50%"  >}}
Wrongly predicts a cat

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397519/fish2_qjdgeu.jpg" width="50%"  >}}
Correctly predicts a fish

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1766397522/fish3_vzdzju.png" width="50%"  >}}
Correctly predicts a fish

So based on a manual test, this model seems to get 4/6 of the images right, which seems to be about 70%. 

Okay, I'll work on tweaking it to make it better in the next post - maybe using a pre-trained model - but for now now, I'll just deploy this model. 