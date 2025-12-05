---
title: "MatPlotLib Basics"
date: 2021-12-20T11:54:37+08:00
# weight: 1
# aliases: ["/first"]
tags: ["Matplotlib"]
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

# Basics of Matplotlib

In this tutorial, I'll be going through the basics of using MatPlotLib.

We'll learn some of the building blocks of MPL: Figures, Artists, Axes, Axis.

### Importing


```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
```

## Figures

The main primitive of MPL is a figure. A figure is an **object class** that contains a lot of methods that we can use to plot figures. In the words of MPL, a figure is a 'top level *Artist*' that holds all plot elements. What is an artist?? I hear you say.

## Artist

An 'Artist' is a base class for objects that render into a FigureCanvas - basically everything visible on a figure is an artist - even `Figure`, `Axes`, and `Axis` objects. Text objects, collections, are all artists.

When the Figure is rendered, all Artists are drawn to the canvas. **Most Artists are tied to an Axes; such an Artist cannot be shared by multiple Axes, or moved from one to another**.

## Creating Figures

To create an empty figure, we do


```python
fig = plt.figure()
```


    <Figure size 432x288 with 0 Axes>


To add a single Axes:


```python
fig, ax = plt.subplots()
```



![png](/notes/20DEC_mpl_images/output_8_0.png)



It is worth looking at the `fig, ax = ...` syntax. This is similar to destructuring in Javascript, where the RHS evaluates to an array or tuple of two items, which is assigned to fig and ax respectively.

To illustrate


```python
ax
```




    <AxesSubplot:>



tells us that `ax` is an AxesSubplot object, whereas


```python
fig
```





![png](/notes/20DEC_mpl_images/output_12_0.png)




returns a figure with type


```python
type(fig)
```




    matplotlib.figure.Figure



---

To create a figure with a 2x2 grid:


```python
fig, axs = plt.subplots(2,2)
```



![png](/notes/20DEC_mpl_images/output_17_0.png)



---

## Axes

An axes is an artist attached to a figure. Usually, graphs come with two Axis objects that provide ticks and tick labels to provide scales for the data.

Each Axes also has a title, set via `set_title()`, and an x-label and y-label set via `set_xlabel()` and `set_ylabel()` respectively.


```python
ax.set_title('Sample Graph')
```




    Text(0.5, 1.0, 'Sample Graph')




```python
fig
```





![png](/notes/20DEC_mpl_images/output_21_0.png)





```python
ax.set_xlabel("Value")
```




    Text(0.5, 3.1999999999999993, 'Value')




```python
ax.set_ylabel("Amount")
```




    Text(3.200000000000003, 0.5, 'Amount')




```python
fig
```





![png](/notes/20DEC_mpl_images/output_24_0.png)




### The main point to note here is that anything that's got to do with adjusting Axes info, you perform operations on the `ax` object.

This includes, setting title of graph, labels.

## Axis

On the other hand, we have `Axis` objects, which set the scale and limits and generate ticks and ticklabels. This is **different** from the `Axes` object.

---

## Input types for plotting functions

To plot data, we need to supply the raw data to be plotted. The plotting functions of MPL `.plot()` expects data of certain type.

1. numpy arrays
2. array-like objects like pandas data objects (but may not work as intended)

Common convention is to convert Dataframes into numpy arrays b4 plotting.

Most methods will also parse objects like dict, or pd.Dataframe.

To plot, you supply data object to the `data` keyword of plotting methods.

e.g.

`ax.scatter('a', 'b', c='c', s='d', data=data)`

# Two ways of plotting in MPL

## 1. OO Style
- explicitly create Figures and Axes,and call methods on them


```python
# Sample Data
x = np.linspace(0,2,100)
```


```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.plot(x, x, label='linear')
```




    [<matplotlib.lines.Line2D at 0x7f852804fc70>]





![png](/notes/20DEC_mpl_images/output_35_1.png)




```python
ax.plot(x, x**2, label='quadratic')
fig
```





![png](/notes/20DEC_mpl_images/output_36_0.png)





```python
ax.plot(x, x**3, label='cubic')
```




    [<matplotlib.lines.Line2D at 0x7f8588590730>]




```python
ax.set_xlabel('x label')  # Add an x-label to the axes.
ax.set_ylabel('y label')  # Add a y-label to the axes.
ax.set_title("Simple Plot")  # Add a title to the axes.
ax.legend();  # Add a legend.
```


```python
fig
```





![png](/notes/20DEC_mpl_images/output_39_0.png)




> In an abstract way, you're creating a figure, and adding 'artist' elements to them step by step.

`plot()` allows you to draw curves.
The rest of the commands are adding descriptions to figure.

## 2. pyplot style

The pyplot style looks like this:

```
plt.figure(figsize=(5, 2.7))
plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
plt.plot(x, x**2, label='quadratic')  # etc.
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend();
```


```python
plt.figure(figsize=(5,2.7))
```




    <Figure size 360x194.4 with 0 Axes>




    <Figure size 360x194.4 with 0 Axes>



```python
plt.plot(x, x, label='linear')
```




    [<matplotlib.lines.Line2D at 0x7f85380738b0>]





![png](/notes/20DEC_mpl_images/output_43_1.png)




```python
plt.plot(x, x**2, label='quadratic')
```




    [<matplotlib.lines.Line2D at 0x7f85280c0f70>]





![png](/notes/20DEC_mpl_images/output_44_1.png)




```python
plt.plot(x, x**3, label='cubic')
```




    [<matplotlib.lines.Line2D at 0x7f85481125b0>]





![png](/notes/20DEC_mpl_images/output_45_1.png)




```python
plt.xlabel('x label')
```




    Text(0.5, 0, 'x label')





![png](/notes/20DEC_mpl_images/output_46_1.png)




```python
plt.figure(figsize=(5, 2.7))
plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
plt.plot(x, x**2, label='quadratic')  # etc.
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend();
```



![png](/notes/20DEC_mpl_images/output_47_0.png)



## Observations

In the pyplot style, you're kind of doing it 'on-the-go'. In contrast to the OO style, you don't create an object assign it to a variable. There seems to be an implicit `plt` object that is called and as observed above, the final figure is only produced when all the `plt` is executed in sequence. It's quite strange. When I executed them one by one, nothing is /notes/20DEC_mpl_images/outputted.

In the documentation, it is advised that generally the OO style is preferred for complicated plots, and the pyplot style be used for quick, interactive works.

# Styling lines

You can style lines of `.plot()` by supplying the following params: `color`, `linewidth`, `linestyle`.

Example:

`ax.plot(x, np.cumsum(data1), color='blue', linewidth=3, linestyle='--')`


```python
plt.figure(figsize=(15,10))
plt.plot([1,2,3,4], [1,4,9,16], "b-", x, x, "r-")
plt.title("First Plot")
plt.xlabel("X label")
plt.ylabel("Y label")
plt.show()
```



![png](/notes/20DEC_mpl_images/output_51_0.png)



### Styling shorthand

Alternatively, you can style your graphs by providing a third optional argument after supplying x and y arguments to `.plot()`, like so:

`plt.plot([1,2,3,4], [1,4,9,16], "g-")`

The above `go` will plot a green line.

To plot blue circles, you use `bo`.

To plot red triangles, you use: `r^`.


```python
plt.plot([1,2,3,4], [1,4,9,16], "r^")
```




    [<matplotlib.lines.Line2D at 0x7f854818ee20>]





![png](/notes/20DEC_mpl_images/output_54_1.png)



# Plotting multiple plots in one figure

We can use `subplot()` method to plot more than one graph in a figure.


```python
plt.subplot(1,2,1)
plt.plot([1,2,3,4], [1,4,9,16], "b-")
plt.title("1st subplot")

plt.subplot(1,2,2)
plt.plot([1,2,3,4], [1,4,9,16], "r^")
plt.title("2nd subplot")

plt.suptitle("Sub Plots")
plt.show()
```



![png](/notes/20DEC_mpl_images/output_56_0.png)



The `subplot()` method takes 3 arguments: `nrows`, `ncols`, and `index`.

In the example above, we wanted 1 row, 2 cols - so we supplied `(1,2,1)` and `(1,2,2)` into the two subplots.

## To plot horizontally stacked graphs

Simply change the row and column arguments to `subplot()`




```python
plt.subplot(2,1,1)
plt.plot([1,2,3,4], [1,4,9,16], "b-")
plt.title("1st subplot")

plt.subplot(2,1,2)
plt.plot([1,2,3,4], [1,4,9,16], "r^")
plt.title("2nd subplot")

plt.suptitle("Sub Plots")
plt.show()
```



![png](/notes/20DEC_mpl_images/output_60_0.png)



# Plotting Subplots OO style

In the example above, we were plotting subplots in the pyplot style. To do so in the OO style, we can do the following:

`fig, ax = plt.subplots(nrows=2, ncols=1, figsize=(6,6))`


```python
fig, ax = plt.subplots(nrows=2, ncols=1, figsize=(6,6))
```



![png](/notes/20DEC_mpl_images/output_63_0.png)



### Filling in data


```python
ax[0].plot([1,2,2,3], [1,5,5,6], "go")
ax[1].plot([1,2,4,3], [1,5,5,6], "r^")
fig
```





![png](/notes/20DEC_mpl_images/output_65_0.png)




### Multiple dimensions Matrix of graphs


```python
fig, ax = plt.subplots(nrows=2, ncols=2, figsize=(6,6))
```



![png](/notes/20DEC_mpl_images/output_67_0.png)




```python
ax[0,1].plot([1,2,2,3], [1,3,4,5], 'go') # top right
ax[1,0].plot(x,x,'r^') # bottom left
fig
```





![png](/notes/20DEC_mpl_images/output_68_0.png)




# Bar Graphs

Same thing as `.plot()` method, but with `.bar()`.


```python
plt.bar([1,2,4,5], [1,3,4,5])
```




    <BarContainer object of 4 artists>





![png](/notes/20DEC_mpl_images/output_71_1.png)



Horizontal bars are done with `barh()` method.

## Stacked Bars

To create stacked bars, we'll need to understand another concept. The first argument we pass to bars will be replaced by the number of bars you want to see, in the form of an array.


```python
index = np.arange(5)
plt.bar(index, [1,3,4,5,6], 0.3)
plt.bar(index+0.3, [1,3,4,5,6], 0.3)

```




    <BarContainer object of 5 artists>





![png](/notes/20DEC_mpl_images/output_75_1.png)



If you want to see 5 bars, supply an index of 5. Then, you simply call the .bar() method twice, and adjust the index of the second bar to be the width of your first bar. `index + 0.3`. This shifts the orange bar to the right.

Finally, you supply the ticks information.


```python
index = np.arange(5)
plt.bar(index, [1,3,4,5,6], 0.3)
plt.bar(index+0.3, [1,3,4,5,6], 0.3)
plt.xticks(index + 0.3/2, [55,65,75,86,98])
```




    ([<matplotlib.axis.XTick at 0x7f8568a5ff10>,
      <matplotlib.axis.XTick at 0x7f8568a5fee0>,
      <matplotlib.axis.XTick at 0x7f8568a5f5b0>,
      <matplotlib.axis.XTick at 0x7f85583a0070>,
      <matplotlib.axis.XTick at 0x7f85583a0700>],
     [Text(0.15, 0, '55'),
      Text(1.15, 0, '65'),
      Text(2.15, 0, '75'),
      Text(3.15, 0, '86'),
      Text(4.15, 0, '98')])





![png](/notes/20DEC_mpl_images/output_78_1.png)



This has the effect of adjusting your ticks so that they're center to both bars. In this case, it's a a rightward adjustment of `0.3/2`. Then, you supply an array of what you want to display.

### Vertically Stacked Bars

In the case of vertically stacked bars, we simply add `bottom` in the `.bar` method that points to what you want to stack on top of.


```python
index = np.arange(5)
plt.bar(index, [1,3,4,5,6], 0.3)
plt.bar(index, [1,3,4,5,6], 0.3, bottom=[1,3,4,5,6])
plt.xticks(index, [1,3,4,5,6] )
```




    ([<matplotlib.axis.XTick at 0x7f85182d53a0>,
      <matplotlib.axis.XTick at 0x7f85182d5370>,
      <matplotlib.axis.XTick at 0x7f85182ae520>,
      <matplotlib.axis.XTick at 0x7f85688c1340>,
      <matplotlib.axis.XTick at 0x7f85688c1a90>],
     [Text(0, 0, '1'),
      Text(1, 0, '3'),
      Text(2, 0, '4'),
      Text(3, 0, '5'),
      Text(4, 0, '6')])





![png](/notes/20DEC_mpl_images/output_82_1.png)



Note that in this case, we don't have to adjust the index width of the second bar graph.

## Pie Charts

To make pie charts, we use `.pie()` method. This method takes the following crucial parameters:

`plt.pie(split_Data, labels=array_of_data_category)`

For instance:


```python
firms = ['Apple', 'Google', 'Microsoft', 'IBM']
market_share = [10, 10, 30, 60]

plt.pie(market_share, labels=firms)
```




    ([<matplotlib.patches.Wedge at 0x7f8528140400>,
      <matplotlib.patches.Wedge at 0x7f8528140940>,
      <matplotlib.patches.Wedge at 0x7f8528140e20>,
      <matplotlib.patches.Wedge at 0x7f8528151340>],
     [Text(1.0554422683381766, 0.30990582150899426, 'Apple'),
      Text(0.720346786112299, 0.8313245501834299, 'Google'),
      Text(-0.4569565739181998, 1.0005951676641962, 'Microsoft'),
      Text(-0.15654617382257757, -1.0888036073881788, 'IBM')])





![png](/notes/20DEC_mpl_images/output_87_1.png)



You can add further modifications of the following:

`Explode`, `shadow`, and `startangle`




```python
Explode=[0,0.1,0,0]
plt.pie(market_share, labels=firms, explode=Explode, shadow=True, startangle=45)
```




    ([<matplotlib.patches.Wedge at 0x7f857a7ead00>,
      <matplotlib.patches.Wedge at 0x7f857a930550>,
      <matplotlib.patches.Wedge at 0x7f857a930ca0>,
      <matplotlib.patches.Wedge at 0x7f857a93f430>],
     [Text(0.5271738771746388, 0.965446893011034, 'Apple'),
      Text(-0.08560705040249196, 1.1969425353463654, 'Google'),
      Text(-1.0306447204031748, 0.3844105361525125, 'Microsoft'),
      Text(0.659205553085804, -0.8805952752433093, 'IBM')])





![png](/notes/20DEC_mpl_images/output_89_1.png)



# Styling

First, you import style from matplotlib.

Then, you use `style.use('ggplot')` to implement style, before plotting. Simple.



```python
from matplotlib import style

style.use('seaborn-pastel')
plt.figure(figsize=(5, 2.7))
plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
plt.plot(x, x**2, label='quadratic')  # etc.
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend();

```



![png](/notes/20DEC_mpl_images/output_92_0.png)



Below are a list of interesting styles:
(https://matplotlib.org/stable/gallery/style_sheets/style_sheets_reference.html)

- fivethirtyeight
- ggplot
- Solarize_Light2
- seaborn
- seaborn-dark
- seaborn-deep
- seaborn-pastel
- seaborn-paper
