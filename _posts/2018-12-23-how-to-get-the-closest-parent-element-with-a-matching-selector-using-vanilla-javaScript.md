---
layout: post
title: How to get the closest parent element with a matching selector using vanilla JavaScript
comments: true
categories: ["tips", "javascript"]
tags: ["tips", "javascript", "jquery"]
---

One thing I need to do a lot in my plugins is find a parent element with a particular selector.

For example, when someone clicks on a link in an accordion, I need to get the parent container for all of the other content sections. I don’t always know exactly what the markup structure will look like, so I can’t just use .parentNode to get it.

In this example, when someone clicks an .accordion-toggle link, I want to get the .accordion element, but I don’t neccessarily know how many nested div’s will be in the markup.

### Creating a helper function 

To do this, we can setup a for loop to climb up the DOM. On each loop, we’ll grab the element’s .parentNode and see if it has our selector. If it doesn’t, we’ll jump to the next .parentNode and repeat.

First, let’s setup a helper function.

<pre>
var getClosest = function () {
	// Code goes here...
};
</pre>

Next, let’s create our loop.

<pre>
var getClosest = function (elem) {
	for ( ; elem && elem !== document; elem = elem.parentNode ) {
		// Do something...
	}
};
</pre>

Normally, you’d set a variable in the first part of your for loop (something like var i = 0), but we don’t need any for this loop.

The next part (between the two semicolons) is where we evaluate our criteria for continuing the loop. We want to make sure that an elem exists and that it’s not the document element (which means we’ve reached the top of the page with no results).

Finally, the last part (after the second semicolon) is where we tell the loop what to do after each iteration. We want to set the elem variable to the current element’s parent (this is how we climb up the DOM).

### Matching by selector 

Inside our loop, we want to check and see if the current element matches our selector. For a class we might use classList. For an ID we’d use elem.id === 'our-id'. And so on. To make things easy, though, let’s use the .matches() method, which checks any valid CSS selector.

<pre>
var getClosest = function (elem, selector) {
	for ( ; elem && elem !== document; elem = elem.parentNode ) {
		if ( elem.matches( selector ) ) return elem;
	}
	return null;
};
</pre>

If there’s a matching element, our helper function will return it. If it makes it through the whole loop without a match, it returns null.

### Browser compatibility 

There’s one last thing we need to do, though. Many browsers implemented .matches() with proprietary prefixes. Others didn’t implement it but support querySelectorAll. In both cases, a lightweight polyfill bolts in .matches() support.

<pre>
var getClosest = function (elem, selector) {

	// Element.matches() polyfill
	if (!Element.prototype.matches) {
	    Element.prototype.matches =
	        Element.prototype.matchesSelector ||
	        Element.prototype.mozMatchesSelector ||
	        Element.prototype.msMatchesSelector ||
	        Element.prototype.oMatchesSelector ||
	        Element.prototype.webkitMatchesSelector ||
	        function(s) {
	            var matches = (this.document || this.ownerDocument).querySelectorAll(s),
	                i = matches.length;
	            while (--i >= 0 && matches.item(i) !== this) {}
	            return i > -1;
	        };
	}

	// Get the closest matching element
	for ( ; elem && elem !== document; elem = elem.parentNode ) {
		if ( elem.matches( selector ) ) return elem;
	}
	return null;

};
</pre>

This gives you browser support back to at least IE9.

### Putting it all together

Now you can do something like this:

<pre>
document.addEventListener('click', function (event) {

	// If the clicked item is an `.accordion-toggle` get the parent `.accordion`
	if ( event.target.classList.contains('accordion-toggle') ) {

		// Get the parent with the `.accordion` class
		var parent = getClosest(event.target, '.accordion');

	}

}, false);
</pre>

Now that you know how this works, you could modify it to things like:

Get all matching parents (not just the first).
Get all parent elements and push them to an array.
Get all parent elements until you find one that matches a selector.
And more!
This helper method is [https://github.com/cferdinandi/getClosest](available on GitHub.)

## References

* [https://gomakethings.com/how-to-get-the-closest-parent-element-with-a-matching-selector-using-vanilla-javascript/](https://gomakethings.com/how-to-get-the-closest-parent-element-with-a-matching-selector-using-vanilla-javascript)