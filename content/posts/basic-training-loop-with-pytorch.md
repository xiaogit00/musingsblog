---
title: "Basic Training Loop With Pytorch"
date: 2025-12-05T18:09:07+08:00
# weight: 1
# aliases: ["/first"]
tags: [""]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
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
After having completed my CS5242 course on neural network and deep learning, I realized that there's a lot more practice that I'll need to do in order to deploy useful models out into the wild. Which means that I would really want to work on and fully master the fundamentals. One of them being Pytorch. 

And so, I began on a Pytorch book by Ian Pointer. 

In the first exercise, I implemented a simple 2 layer neural network to classify whether an image is a cat or a fish. This is not new to me, but still, I think there's a couple of things about this implementation that I should learn about:

### Code for training a fish/cat classifier
```python
# %%
import torch
import torchvision
from torchvision import transforms
from torch.utils.data import Dataset, DataLoader
import torch.nn as nn
import torch.nn.functional as F

# %%
train_data_path = './train/'

transforms = transforms.Compose([
    transforms.Lambda(lambda img: img.convert("RGB")),
    transforms.Resize((64, 64)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225])
]) # defines a fn that chains together a series of transformations; 1) resizes to 64 x 64, convert image to tensor, normalize the tensor around a set of mean and std deviation

train_data = torchvision.datasets.ImageFolder(root=train_data_path, transform=transforms)

# %%
val_data_path = "./val/"
val_data = torchvision.datasets.ImageFolder(root=val_data_path, transform=transforms)

test_data_path = "./test/"
test_data = torchvision.datasets.ImageFolder(root=test_data_path, transform=transforms)


# %%
batch_size=64
train_data_loader = DataLoader(train_data, batch_size=batch_size)
val_data_loader = DataLoader(val_data, batch_size=batch_size)
test_data_loader = DataLoader(test_data, batch_size=batch_size)

# %%
class SimpleNet(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(12288, 84)
        self.fc2 = nn.Linear(84, 50)
        self.fc3 = nn.Linear(50,2)
    
    def forward(self, x):
        x = x.view(-1, 12288)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
    
simplenet = SimpleNet()

# %%
simplenet

# %%
import torch.optim as optim
optimizer = optim.Adam(simplenet.parameters(), lr=0.001)

# %%
if torch.cuda.is_available():
	device = torch.device("cuda")
else:
	device = torch.device("cpu")


# %%
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
from PIL import Image
import os

def delete_corrupted_images(folder):
    for filename in os.listdir(folder):
        filepath = os.path.join(folder, filename)

        # Skip directories
        if not os.path.isfile(filepath):
            continue

        try:
            with Image.open(filepath) as img:
                img.verify()  # Check if the image is valid
        except Exception as e:
            print(f"Deleting corrupted image: {filename} ({e})")
            os.remove(filepath)



# %%
path = "./val/fish"
delete_corrupted_images(path)

# %%
train(simplenet, optimizer, torch.nn.CrossEntropyLoss(),train_data_loader, test_data_loader, 20, device)

# %%

from PIL import Image
labels = ['cat','fish']


# %%
FILENAME = './myImageTest/fish5.png'
img = Image.open(FILENAME)
# img.getbands()
img = transforms(img)
img = img.unsqueeze(0)
prediction = simplenet(img)
prediction = prediction.argmax()
print(labels[prediction])


#%% 
torch.save(simplenet, "./golden/fishcat_classifier")

```
In the rest of this post, I'll pick out individual pieces of the implementation above that I feel are worth learning. 

### 1. Preprocessing using Image `transforms`
To process the raw images, we convert them into tensors of right dimensions using a `transforms` function from `torchvision`. Essentially, this function allows  you to define the kinds of transformations needed:

```python

transforms = transforms.Compose([
    transforms.Lambda(lambda img: img.convert("RGB")),
    transforms.Resize((64, 64)),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225])
])

```

`Compose` strings together a few functions that individually does the following:
1. First, it checks the number of channels of the image (RGB, or RGBA[^1]?)
2. Then, it resizes to 64x64. 
3. It converts the image to a tensor
4. And then normalizes[^2]  it. 


This function expect a list of functions within the `transforms` module. Interestingly, each of these parameters are of the nn.Module base class. It therefore has a `forward()` function:

```python
#class Resize:
def forward(self, img):
        """
        Args:
            img (PIL Image or Tensor): Image to be scaled.

        Returns:
            PIL Image or Tensor: Rescaled image.
        """
        return F.resize(img, self.size, self.interpolation, self.max_size, self.antialias)
```

The eventual chain of calls will essentially be passing the input `img` through each of those functions, as evident in this loop within the `__call__` method of the Compose class:
```python
# Implementation of the __call__ fn of Compose:
# class Compose:
    def __call__(self, img):
        for t in self.transforms:
            img = t(img)
        return img
```
By defining your image transformers in this way, you are able to preprocess them. 

### 2. Dealing with folders of Images

In this approach, I have downloaded the images and structured them in the following manner:

```markdown
- /train
    - cat
    - fish
- /val
    - cat
    - fish
- /test
    - cat
    - fish
```
 

Constructing your image dataset in this manner will enable you to leverage torchvision's `ImageFolder` class:
```python
train_data = torchvision.datasets.ImageFolder(root=train_data_path, transform=transforms)
```
This is a class that allows you to 'import' image data from folders, creating `Dataset` objects. 

Inspecting `train_data: ImageFolder`:

```python
Dataset ImageFolder
    Number of datapoints: 767
    Root location: ./train/
    StandardTransform
Transform: Compose(
               Resize(size=64, interpolation=bilinear, max_size=None, antialias=True)
               ToTensor()
               Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])
           )

```

The `ImageFolder` object is essentially an array. You can do `train_data[0]` and it'll return you a tensor tuple, of X and Y value: 

```python
(tensor([[[-0.6965, -0.6965, -0.6109,  ..., -0.2856, -0.3027, -0.4397],
          [-0.6965, -0.6794, -0.5938,  ..., -0.3027, -0.2684, -0.3883],
          [-0.6281, -0.5938, -0.5767,  ..., -0.3027, -0.3027, -0.3369],
            ...    
            [ 0.3742,  0.3916,  0.4614,  ..., -1.0898, -0.8458, -0.7936],
          [ 0.3219,  0.3916,  0.4091,  ..., -1.2293, -1.1421, -0.8458],
          [ 0.3219,  0.4265,  0.4265,  ..., -1.2293, -1.2641, -1.1596]]]),
 0)
```

This object has certain methods, like `ImageFolder.samples`, `ImageFolder.classes`, `ImageFolder.targets`. Samples will give you the file name, classes the categories, and targets an array of labels. 

You instantiate an `ImageFolder` object for each dataset (train, test, val), and then in the next step, you feed them into DataLoaders:

```python
batch_size=64
train_data_loader = DataLoader(train_data, batch_size=batch_size)
```

The DataLoader is simply a way to mini-batch the training data. During the training, you iterate through the DataLoader to extract input and labels, before doing forward pass, backward pass, and update. 

```python
for epoch in range(epochs):
	for batch in train_loader:
		optimizer.zero_grad()
		input, target = batch
		output = model(input)
		loss = loss_fn(output, target)
		loss.backward()
		optimizer.step()

```

### 3. Defining the model

```python
class SimpleNet(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(12288, 84)
        self.fc2 = nn.Linear(84, 50)
        self.fc3 = nn.Linear(50,2)
    
    def forward(self, x):
        x = x.view(-1, 12288)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
    
simplenet = SimpleNet()
```
Here, the only thing worth noting is the dimensions. In the first linear layer, the inputs is 12288, because each single data image is of dimension 3x64x64 = 12288. To validate, you do `train_data[0][0].shape`. This is quite an important point to note about your linear layer construction: with each iteration of the forward pass, you're essentially passing in a *single data point* into the model.  

#### The view function: 
Within the `forward` method itself, the function `.view(-1, 12288)` transforms a shape of (3, 64, 64) into (1, 12288). What is this syntax `.view(-1, 12288)`? `.view` will reshape the tensor into however many dimensions you provide in the arguments, which, in this case, is 2. The `-1` in the first argument tells Pytorch that fill this dimension up based on whatever that's defined in the other dimensions. In this case, given that the second dimension is 12288, the first dimension will be 1. 

Just to illustrate this further, if you were to run `.view(-1, 64)` on the same tensor, you'll get dimensions `(192, 64)`, because a tensor of shape `(3, 64, 64)` will be compressed to: `(3x64, 64)`. 


### 4. Defining the optimizer 

This part is relatively straight forward. You just instantiate an optimizer object using `torch.optim` module:


```python
import torch.optim as optim
optimizer = optim.Adam(simplenet.parameters(), lr=0.001)
```

Also, you'll need to define the training logic to use GPUs whenever possible:


```python
if torch.cuda.is_available():
	device = torch.device("cuda")
else:
	device = torch.device("cpu")

```

### 5. The training loop

Finally, you define the training loop like so: 

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
```

It's quite a straightforward training loop, but there's a couple of things to take note of. 

First, you see that the `training_loss` and `valid_loss` are defined once in each epoch. Which means that both the training and validation loss are specific to each epoch. That makes sense: you're trying to reduce the loss with the weights you have, for each epoch. 

Second, the iterator object, `train_loader`, returns a `batch` each iteration, which further decomposes to `inputs` and `targets`. `inputs` is a matrix of shape (B, C, W, H). When you defined `batch_size=64` earlier, that will be the value of B. On the other hand, C, W, H are 3, 64, 64 respectively, pretty straightforward. 

Third, here's quite a subtle but important point: after extracting your inputs and targets each batch, you move them to the GPU, if there is one. 

Fourth, the `loss_fn` takes the general arguments, `outputs, target`. It compares between the two to produce a loss value. In my actual implementation, the loss function passed in is `torch.nn.CrossEntropyLoss()`. 

Fifth, you call `loss.backward()` to calculate the differential of gradients wrt loss, and then you update the weights using `optimizer.step()`. 

Sixth, the way you update the training loss is as follows: `training_loss += loss.data.item() * inputs.size(0)`. What this suggests is that within each batch, you're taking the loss value times the batch size, and accumulating it into `training_loss`. Finally, outside of each batch, you're finding the "unit" training loss by taking the total `training_loss` and dividing by the number of data points. 

Seventh, you switch the model to eval mode, and for each batch in the `val_loader`, you pass the inputs to the GPU, does forward pass on the inputs, define the same loss function and calculate `valid_loss`, and then calculates the num_correct and further compute the accuracy. This allows you to see how well the model is performing on the validation set every epoch, where the validation set does *not* influence the training process. 

### Summary
This turned out to be quite a lengthy post, but I think it's quite a good recap on some of the subtleties along the way of a basic training loop. For my model, unfortunately I can only get the accuracy up to 70%. In the next post, I'll be implementing different models and see how they fare compared to this baseline.

---
[^1]: Apparently, "A" in RGBA stands for Alpha, a value between 0-1 that determines the opacity of an image. Whilst running inference with my model, I encountered this problem where a screenshot of a cat I took with my laptop cannot be passed into the `transforms` function. The error was quite interesting: `RuntimeError: The size of tensor a (4) must match the size of tensor b (3) at non-singleton dimension 0`. It turns out, that it's because my image came in RGBA, whilst my Normalize function expects 3 channels. And thus, there wasn't a match. 

[^2]: The values tend to range from 0-255. If we don't normalize it, the values may explode if we multiply it too many times. By keeping values between 0 and 1, we avoid the exploding gradient problem.