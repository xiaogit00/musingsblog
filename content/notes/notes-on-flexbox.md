---
title: "Notes on Flexbox"
date: 2021-08-24T06:06:17+08:00
# weight: 1
# aliases: ["/first"]
tags: ["css","flexbox"]
author: "Lei"
# author: ["Me", "You"] # multiple authors
showToc: true
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
Flexboxes are getting more and more important in designing responsive web pages. In my case, I need it to help design my left navigation bar for my app. Here are some notes taken from the video below for reference.

{{< youtube fYq5PXgSsbE >}}

****

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629756895/Screenshot_2021-08-24_at_6.14.40_AM_vgmyqr.png" width="20%" caption="Containers and items without flex">}}

Below is the HTML and CSS:

```html
<!-- HTML Elements -->
<div class="flexbox-container">
  <div class="flexbox-item flexbox-item-1"></div>
  <div class="flexbox-item flexbox-item-2"></div>
  <div class="flexbox-item flexbox-item-3"></div>
</div>
```

```css
/* CSS elements */
.flexbox-container {

}

.flexbox-item {
  width: 200px;
  margin: 10px;
  border: 3pm solid #333;
  background-color: #dfdfdf;
}

.flexbox-item-1 {
  min-height: 100px;
}

.flexbox-item-2 {
  min-height: 200px;
}

.flexbox-item-3 {
  min-height: 300px;
}
```

### Flex container: `display:flex`
When we add the following line:

```css
.flexbox-container {
  display: flex;
}
```

All the items are distributed evenly across the page.

![After flexbox added](https://res.cloudinary.com/dl4murstw/image/upload/v1629757168/Screenshot_2021-08-24_at_6.19.20_AM_h8szxj.png)

>### The main thing about Flexboxes is that..

*It allows you to style the flexibility and sizing of different elements in the container from the container selector without ever having to style the flexbox items.*

Most of the time, you will be laying out different elements and aligning them across the container. You also specify how they grow and shrink inside the container.

### Alignment: `justify-content:center`
Within the container selector, you can add `justify-content:center` to center the elements.
```css
.flexbox-container {
  display: flex;
  justify-content:center;
}
```
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629759096/Screenshot_2021-08-24_at_6.51.27_AM_wd3zp1.png" width="80%">}}

If we want to lay out items with space between them, we can use `justify-content: space-between`. The output:  
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629759307/Screenshot_2021-08-24_at_6.55.01_AM_w3sble.png" width="80%">}}

`justify-content:space-around` puts an even amount of space on all sides of flex items.
{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629759389/Screenshot_2021-08-24_at_6.56.24_AM_vcfvte.png" width="80%">}}

### Changing direction: `flex-direction:column`
`flex-direction: column`
Just adding this at the top cos this is one of the most used. It changes your items from horizontal to vertical. Good for left nav bars.

### Growing / Shrinking items `align-items:stretch`

By default, the container selector has `align items: stretch` applied to it. This property will make your items 'grow' to full size, along the cross axis. The cross axis is defined as the direction that is perpendicular to the direction of your flex-container. e.g. if your flex is vertical, then stretch will make it apply to horizontal.

You can make them keep their original size with `align-items: flex-start`  

### `align-items: flex-start`

{{< figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629759672/Screenshot_2021-08-24_at_7.01.05_AM_ldaegw.png" width="80%">}}

### Centering items `align-items:center`
This is one of the most important things you can do with flexbox.

`align-items: center`

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629759937/Screenshot_2021-08-24_at_7.05.29_AM_thhqse.png" width="80%">}}

Example use case: you want all your left nav bars to be aligned along center.

### Wrapping upon shrinking viewport: `flex-wrap:wrap`

By default, when you shrink the viewport, the flexbox items will all shrink their sizes. However, with `flex-wrap:wrap` within container selector, the items will wrap around one another when you shrink.

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760119/Screenshot_2021-08-24_at_7.08.30_AM_wypyte.png" width="30%">}}

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760160/Screenshot_2021-08-24_at_7.09.16_AM_gcso9q.png" width="20%">}}

When you wrap, the `align-content: center` property tells it how much space to put between the lines, as well as where to place the content.

`align-content:flex-end`
{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760345/Screenshot_2021-08-24_at_7.12.18_AM_z2cjyt.png" width="20%">}}

`align-content:flex-start`
{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760385/Screenshot_2021-08-24_at_7.13.01_AM_mgk5fk.png" width="20%">}}

`align-content:space-between`
{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760434/Screenshot_2021-08-24_at_7.13.50_AM_onuad5.png" width="20%">}}

But generally, `align-content` isn't used that often: `justify-content` and `align-items` will do for most of the time.

---
Next, you have your individual flexbox-item selectors. The properties applied here will override the container properties.

### Preventing one item from shrinking `flex-shrink:0`

Adding `flex-shrink:0` to flex-item selector will ensure that particular item doesn't shrink when page shrinks. (Good for left nav)

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760928/Screenshot_2021-08-24_at_7.22.03_AM_bf1iva.png" width="60%" caption="Applied on first item">}}

`flex-grow: 1`
This will ensure that when viewport expands, that particular item expands alongside it.
(Good for the right pane of my app)

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629760879/Screenshot_2021-08-24_at_7.21.14_AM_jrqndi.png" width="80%" caption="Applied on third item" >}}

### Different rates of growth

On Item 2, we have `flex-grow: 2`, and on Item 3, we have `flex-grow: 1`. When the page expands, 2 will grow at twice the rate of 3.

{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629761195/Screenshot_2021-08-24_at_7.26.29_AM_tc70ge.png" width="80%" >}}

### Aligning individual items by overriding
`align-self` property can be used on individual flex items to override container alignments.

```css
.flexbox-item-2 {
  align-self: center;
}
```
{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629761481/Screenshot_2021-08-24_at_7.31.16_AM_xrp8wp.png" width="70%" >}}
&nbsp
```css
.flexbox-item-1 {
  align-self: flex-end;
  /* Instead of stretching */
}
```
{{<figure src="https://res.cloudinary.com/dl4murstw/image/upload/v1629761536/Screenshot_2021-08-24_at_7.32.06_AM_pgdt0w.png" width="70%" >}}

### Ordering
`order` property within flex-items allows us to change the order of the items without changing the html.

within flexbox-item1, `order: 3;` will set it all the way to the right.

But most of the time, don't use the order property cos it messes up the flow for people using screen readers.

### Shorthand for flex, shrink, grow: `flex: 1 0 0px`

`flex: 1 0 0px`
(grow, shrink, basis)
