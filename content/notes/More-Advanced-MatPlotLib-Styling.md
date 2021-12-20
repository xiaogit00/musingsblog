---
title: "More Advanced MatPlotLib Styling"
date: 2021-12-20T12:44:49+08:00
# weight: 1
# aliases: ["/first"]
tags: ["MatPlotLib"]
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

So, going more into depth with MatPlotLib is all about learning how to style your graphs according to how you'd like them.

I'll document some more techniques that I've learned here.

# Creating a secondary axis
Sometimes, you need to overlay graphs. In my specific case, I needed a graph that showed both Value and Amount. My left y-axis would be the Value, whilst my right y-axis would be Amount.

It looks like this

![](https://res.cloudinary.com/dl4murstw/image/upload/v1639975699/graph_toocfd.png)

How do I do that?

Step 1:  
Use the `.twinx()` method on the axes.

```python {hl_lines=[2]}
fig, ax = plt.subplots(figsize=(10,8))
ax2 = ax.twinx()
```
This will create a new y-axis that shares the x-axis, on the opposite side.

Step 2:  
Define `ax2`
```python
ax2.bar(df2.index, df2.Amount, label="Amount", color="orange", alpha=0.2)
ax2.grid(False)
```

Step 3:   
Adjust the ax2 ticks to what you need.

```python
y_ticks = np.linspace(0.001, 0.010, 10)
ax2.set_yticks(y_ticks)
```

That's it!

# Changing transparency of lines
To change transparency of graph, use `alpha` param.
```python
ax2.bar(df2.index, df2.Amount, label="Amount", alpha=0.2)
```

# Removing the grid   
To remove grid, find the axes you want to target, and remove.
`ax2.grid(False)`

# Scaling your graph (Setting ticks)
To scale your graph, you need to set ticks for the relevant axis. Use the `ax.set_yticks(array)` method to set the ticks, which has the effect of scaling your graph.  
```python
ax2.set_yticks(y_ticks)
```

# Generating range of evenly spaced numbers using numpy
`y_ticks = np.linspace(0.001, 0.010, 10)`  
This generates 10 numbers, evenly spaced between 0.001 to 0.010.

# Legends for entire chart
When you have multiple axes, your legends might overlap. To have a good looking legend, you can use `fig.legend()`

To change position of legend, do: `fig.legend(loc="upper right")`

# Fixing dates formatting on x-axis
The dates tend to look pretty weird due to overlaps. A quick fix is this:

`fig.autofmt_xdate()`
---

That's it for this chapter.
