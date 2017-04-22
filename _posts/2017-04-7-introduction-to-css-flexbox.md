---
layout: post
title: Introduction to CSS Flexbox
comments: true
categories: [css]
tags: [css, flexbox]
---

If you ever wrote CSS to align and position elements you probably ran into some issues, which is totally normal because it wasn't always easy. At blogfoster, we often found ourselves writing weird hacks like adding unnecessary containers, using floats in too many places and having absolutely positioned children in relatively positioned parents.

Thankfully, it doesn't have to be like that anymore. With Flexbox, we can solve most layout problems with a few lines of code. If you have not adready spent more time learning it, this post will give you an overview of all the properties you need to know. If you've aldready familiar with Flexbox you can also jumb right to the end and have a look at some real world examples.

## Flex Contriner Properties

+ `display: flex`

	Once you set `display: flex` on your container element, all flex properties are applied with their default values to your container and it's direct children (flex items). We are going to have a detailed look at all properties and find out why for instance after only setting `display: flex` suddenly all flex item align side by side.

+ `flex-direction: row | column | row-reverse | column-reverse`

	Every flex container has a _main axis_ defining the direction flex items are placed. By default `flex-direction` is set to `row`, which means that the main axis goes from _left-to-right_. If you have it to set `column` the elements would stack from *top-to-bottom* as they would do without using FlexBox.

* `justify-content: flex-start | flex-end | center | space-between | space-around`

	We can use `justify-content` to *distribute* our flex items along the main axis. By default, it's set to `flex-start`. Be aware that `flex-direction` directly affects `justify-content`. `justify-content: flex-start` here means pack all flex items to start of the main axis which is why we see all flex items on the left by default. `flex-end` packs everything to the right. `center`, well, centers the items but interesting ones are `space-between` and `space-around`. `space-between` distributes the remaining space evenly between the items, `space-around` does the same thing, only this time, the first and  last items don't have any space towards the border of the container.

* `align-items: flex-start | flex-end | center | space-between | space-around`

	`align-items` is basically the same as `justify-content` only for the _cross axis_. If the main axis is horizontal, the cross axis is verfical (top-to-bottom), if the main axis is vertical, the cross axis is horizontal (left-to-right). `align-items: flex-start | flex-end | center` are self-explanatory and  `baseline` is useful if you want your items text content to be aligned.

* `flex-wrap: nowrap | wrap | wrap-reverse`

	Let's assume all our flex items have explicit widths set to `30%`. If we have more than 3 items in the container, normally we'd think that the 4th item wouldn't fit the container and should either overlow or break into the next row. However, because `flex-wrap` is by default set to `nowrap` that doesn't happen. If we use `flex-wrap: wrap` the 4th item break into a second row.

## References

+ [http://engineering.blogfoster.com/flexbox-introduction/](http://engineering.blogfoster.com/flexbox-introduction/)
