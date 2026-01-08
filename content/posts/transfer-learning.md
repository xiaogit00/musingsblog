---
title: "Transfer Learning"
date: 2026-01-08T20:08:58+08:00
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
Training a CNN from scratch only got my accuracy up till 70%, whereas with transfer learning, I managed to get my accuracy up to 90%. 

What do I mean by transfer learning here? It means that I used a Resnet50 pretrained model as a departure point, and only fine tuned the final linear layers. 

The entire model implementation looks like this: 

```python
def build_model(num_classes=2, freeze_backbone=True):
    transfer_model = models.resnet50(weights='DEFAULT')

    if freeze_backbone:
        for param in transfer_model.parameters():
            param.requires_grad = False

    transfer_model.fc = nn.Sequential(
        nn.Linear(transfer_model.fc.in_features, 500),
        nn.ReLU(),
        nn.Dropout(),
        nn.Linear(500, num_classes)
    )
    for param in transfer_model.fc.parameters():
        param.requires_grad = True
        
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    transfer_model = transfer_model.to(device)

    return transfer_model
```

Here, I rely on a `build_model` function to instantiate my model. 

However, whilst implementing this, I faced several problems. I'll depict a couple of problems here because it's very likely I'd face it again, and I'll need to ensure that I understand what exactly is wrong. 

## Debugging a training loop with diverging accuracy

So in my code, I faced this problem: I had a training loop where accuracy fell from 0.9 to 0.1. I asked Deepseek, and it gave me a version that resulted in 100% accuracy. Given that the model is the same, what exactly is the difference? 

Here is the training loop that diverged:

```python
def train(model, optimizer, loss_fn, train_loader, val_loader, epochs=20, device="cpu"):
    for epoch in range(epochs):
        training_loss = 0.0 # each epoch has their own training loss.. 
        valid_loss = 0.0 # ..and validation loss
        model.train() # set it to training  mode
        for batch in train_loader: # to find how many batches, train_data_loader.__len__()
            optimizer.zero_grad() # what this does beneath the hood is that it loops through all the params of model and sets them to grad 0
            inputs, targets = batch # inputs is matrix of (B, C, W, H), targets (B, 1)
            inputs = inputs.to(device)
            targets = targets.to(device).long()
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
            preds = torch.argmax(output, dim=1)
            targets = targets.view(-1).long()
            correct = (preds == targets) #compare all prediction in batch to ground truth
            num_correct += torch.sum(correct).item() # num correct in batch
            num_examples += correct.shape[0] #examples in batch
        #at this point, valid_loss is total loss of entire epoch
        valid_loss /= len(val_loader.dataset)

        print('Epoch: {}, Training Loss: {:.2f}, Validation Loss: {:.2f}, accuracy = {:.2f}'.format(epoch, training_loss,
        valid_loss, num_correct / num_examples))


```
So I was trying to troubleshoot what is wrong with this code. The first thing is that I am defining targets twice in the validation loop. I need to take the second one out. Then, apparently, targets need to be cast to `long`, like so: 

`targets = targets.to(device).long()`

Now, targets is a tensor of shape [64]. However, what's the dtype of targets? When I printed it, it's `torch.int64`, which is already long. 

However, I think this is a case where certain earlier Pytorch versions or data loaders returned different dtypes, so making it explicit here will be better for debugging. 

The reason why long is required is because most Pytorch classification loss functions like CrossEntropyLoss require target indices to be `torch.long`. 

So just by changing this, I am already achieving accuracy of 90%. 

### A super important lesson here is to be really conscientious of what the shape, devices, and dtypes each of the variables. There should be a way to make things as explicit as possible in your code, and that depends pretty much on your mastery and understanding of concepts. 

If your understanding is hazy, you're going to be met with problems down the road. 

### Problem 2 with my code: 
Another optimization that the deepseek code made is with target reshaping. They added this to each batch:

```python
for batch in train_loader:
            ...
            # Ensure targets are the right shape for loss function
            if len(targets.shape) > 1 and targets.shape[1] == 1:
                targets = targets.squeeze(1)  # Remove extra dimension if present
```

So here, target shape tend to be single dimension. For instance, the `len` of `torch.Size([64])` is actually 1, which is the dimension of the matrix. If your matrix is of shape (64, 1), then the len is 2. 

What this code is saying, is that for the cases where the shape looks like (64, 1), remove the extra '1' dimension. Note: here, squeeze actually removes only extra dimensions that is 1. For instance: 

```python
In [46]: y = torch.rand(3, 1)

In [47]: y
Out[47]: 
tensor([[0.7615],
        [0.3994],
        [0.6044]])

In [48]: y.squeeze(1)
Out[48]: tensor([0.7615, 0.3994, 0.6044])

In [49]: y.squeeze(1).shape
Out[49]: torch.Size([3])

# On the flip side:
In [50]: y2 = torch.rand(3, 2)

In [51]: y2.squeeze(1).shape
Out[51]: torch.Size([3, 2]) # still retains the shape. 
```

The reason for this move is that:

```python
# Some datasets/libraries load targets with an extra dimension:
targets_2d = torch.tensor([[0], [1], [2], [3]])  # Shape: [4, 1]
print(targets_2d.shape)  # torch.Size([4, 1])

# But most PyTorch loss functions expect 1D targets:
targets_1d = torch.tensor([0, 1, 2, 3])  # Shape: [4]
print(targets_1d.shape)  # torch.Size([4])
```

Again, this is a defensive coding technique that'll greatly reduces hard to detect bugs, especially if you're constantly moving your code between environments. 

A lot for me to take away from this seemingly simple implementation. 

Two core takeaways: 1) it pays off to do your due diligence in terms of understanding exactly what's going on under the hood behind each operation - I am rusty on that front; 2) it's important to implement defensive coding principles so that you can save time in the long run as your code executes well in different environments. 