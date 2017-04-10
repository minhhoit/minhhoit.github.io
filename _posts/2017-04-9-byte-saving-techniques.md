---
layout: post
title: Byte saving techniques
categories: [jquery, javascript, performance]
tags: [jquery, javascript]
---

This is a collection of Javascript wizardry that can shave of your code.

## Arguments
---

### Use one-letter positional arguments, in alphabetical order

Since arguments will need to be as short as posible, and will likely be reused with their lifetime, it's best to treat theme as positionals instead of trying to give theme meaning through their name. While using one-leter names mariginally aids readability for a single function, keeping a consitent approach helps readability across all functions.

<pre>
function(t,d,v,i,f){...} //before
function(a,b,c,d,e){...} //after
</pre>

### Test aruments presence instead of length

Use `in` to check whether a given arument was passed

<pre>
arguments.length>1||(cb=alert) // before
1 in arguments||(cb=alert)     // after
</pre>

If only truthy arguments are of interest, you can even boil that down to
<pre>
arguments[0]&&(cb=alert)       // works only if arguments[0] coerces to true
Embed functionality within arguments
</pre>

Save delimiters by processing stuff within (unused) arguments

<pre>
a+=b<<1;x(a,1); // before
x(a+=b<<1,1);   // after
</pre>

### Reuse parenthesis of the function call

There are some functions which take no argument, and obviously you can reuse the parentheses when calling them. See @snowlord's RPN function.
<pre>
((a=b.pop(),b.pop())+c+a); // before
(b.pop(a=b.pop())+c+a);    // after
</pre>

If you're not sure if a function really takes no arguments, see if its .length is 0.

### `setInterval` and `setTimeout` hacks

Use strings instead of functions in setInterval and setTimeout.
<pre>
setInterval(function(){console.log("z")},100) // before
setInterval('console.log("z")',100) // after
</pre>
`setInterval` and `setTimeout` default to the browser's fastest possible time interval (usually 1ms in most modern browsers) if the second argument is left out.
<pre>
setInterval('console.log("z")',1) // before
setInterval('console.log("z")') // after
</pre>

## Variables
---

### Use placeholder arguments instead of var

Save bytes on the `var` declaration by putting placeholder arguments in the function declaration.
<pre>
function(a){var b=1;...} // before
function(a,b){b=1;...}   // after
</pre>

Please be careful as sometimes var declaration is shorter. Take the right decision in each case.

<pre>
function(a,b,c,d){b=1;c=2;d=3;...} // before
function(a){var b=1,c=2,d=3;...}   // after
</pre>

### Re-use variables where possible

Careful reuse of a variable that is no longer needed can save bytes.
<pre>
setTimeout(function(){for(var i=10;i--;)... }, a) // before
setTimeout(function(){for(a=10;a--;)... }, a)     // after
</pre>

### Assign wherever possible

Since assignment returns the assigned value, perform assignment and evaluation at the same time to save bytes. A good example of this is @jed's JSONP function, where the string script is assigned in the createElement method.

<pre>
a=this.localStorage;if(a){...} // before
if(a=this.localStorage){...}   // after
</pre>

### Use an array to swap variables

An array can be used as a temporary placeholder to avoid declaring another variable.
<pre>
var a=1,b=2,c;c=a;a=b;b=c // before
var a=1,b=2;a=[b,b=a][0]  // after
var a=1,b=2;a=b^a^(b=a)   // after - not as useful, but can come in handy
</pre>

Alternatively, for numbers you can save another two bytes:
<pre>
var a=1,b=2;a=[b,b=a][0]  // before
var a=1,b=2;a+=b-(b=a)    // after
</pre>

### Exploit coercion

JavaScript coercion is a blessing and a curse, but sometimes it can be very useful. @jed's pubsub function decrements a negative variable, and then concatenates the results with a string, resulting in a string like someString-123, which is exploited later by using the hyphen as a split token to return the original string.

### Choose small data format

Required data will often be represented as Array or Object. In many cases, these byte-hungry formats can be replaced by strings. The Date.parse polyfill shows a great example of a conversion table that'd usually be an Object.

## Loops
---

### Omit loop bodies

If you can perform all the logic you need within the conditional part of a loop, you don't need the loop body. For an example, see @jed's timeAgo function.

### Use for over while

`for` and `while` require the same number of bytes, but for gives you more control and assignment opportunity.

<pre>
while(i--){...} // before
for(;i--;){...} // after

i=10;while(i--){...} // before
for(i=10;i--;){...}  // after
</pre>

FYI, the second argument to a for-loop can be omitted, too - it will only stop the loop if it returns anything false-y at all.

### Use index presence to iterate arrays of truthy items

When iterating over an array of objects that you know are truthy, short circuit on object presence to save bytes.

<pre>
for(a=[1,2,3,4,5],l=a.length,i=0;i<l;i++){b=a[i];...}
for(a=[1,2,3,4,5],i=0;b=a[i++];){...}
</pre>

### Use for..in with assignment to get the keys of an object

<pre>
a=[];i=0;for(b in window)a[i++]=b // before
a=[];i=0;for(a[i++]in window)     // after
a=Object.keys(window)             // ES6 (not same as key in window, only returns own)
</pre>

Coercion Hint: you can coerce the counter from an array: `i=a=[];for(a[i++]in window);`

### Use reverse loops where possible

If an array can be iterated reversely, it may save some bytes:

<pre>
for(a=0;a<x.length;a++)...     // before
for(a=x.length;a--;)...        // after
</pre>

Use both for body and counting expression for multiple operations

<pre>
for(i=3;i--;foo(),bar());   // before
for(i=3;i--;)foo(),bar();   // before
for(i=3;i--;bar())foo();    // after
for..in will not iterate over false - use this to trigger iteration
</pre>

If for..in encounters anything but an object (or string in any browser but ye olde IE), e.g. false or 0, it will silently continue without iteration.

<pre>
if(c)for(a in b)x(b[a]); // before
for(a in c&&b)x(b[a]);   // after
</pre>

## Operators
---
### Understand operator precedence

This Mozilla page is an excellent resource to get started.

### Understand bitwise operator hacks

### Use `~` with indexOf to test presence

<pre>
hasAnF="This sentence has an f.".indexOf("f")>=0 // before
hasAnF=~"This sentence has an f.".indexOf("f")   // after
</pre>

### Use `,` to chain expressions on one conditional line

<pre>
with(document){open();write("hello");close()}
with(document)open(),write("hello"),close()
</pre>

### Use `[]._` instead of undefined

`""._, 1.._` and `0[0]` also work, but are slower. void 0 is faster than undefined but longer than the alternatives.

### Remove unnecessary space after an operator

Whitespace isn't always needed after an operator and may sometimes be omitted:

<pre>
typeof [] // before
typeof[]  // after
</pre>

## Numbers
---

###Use `~~` and `0|` instead of `Math.floor` for positive numbers

Both of these operator combos will floor numbers (note that since ~ has lower precedence than |, they are not identical).

<pre>
rand10=Math.floor(Math.random()*10) // before
rand10=0|Math.random()*10           // after
</pre>

If you are flooring a quotient where the divisor is a multiple of 2, a bit-shift-right will perform both operations in one statement:

<pre>
Math.floor(a/2) // before
a>>1            // after

Math.floor(a / 4) // before
a>>2            // after
Use A + 0.5 | 0 instead of Math.round for positive numbers

Math.round(a) // before
a+.5|0        // after
</pre>

Also, for negative number just change `+.5|0 to -.5|0`

<pre>
Math.round(-a) // before
-a-.5|0        // after
</pre>

### Use AeB format for large denary numbers

This is equivalent to `A*Math.pow(10,B)`.

<pre>
million=1000000 // before
million=1e6     // after
</pre>

Use `A<<B` format for large binary numbers

This is equivalent to `A*Math.pow(2,B)`. See @jed's [rgb2hex](https://gist.github.com/983535) for an example.

<pre>
color=0x100000 // before
color=1<<20    // after
</pre>

### Use `1/0` instead of Infinity

It’s shorter. Besides, division by zero gets you free internet points.

<pre>
[Infinity,-Infinity] // before
[1/0,-1/0]           // after
</pre>

### Use division instead of isFinite()

Division of 1 by any finite number results nonzero "truthy" value.

<pre>
if(isFinite(a)) // before
if(1/a)         // after
</pre>

### Exploit the "falsiness" of 0

When comparing numbers, it's often shorter to munge the value to 0 first.

<pre>
a==1||console.log("not one") // before
~-a&&console.log("not one")  // after
</pre>

### Use `~` to coerce any non-number to -1,

Used together with the unary -, this is a great way to increment numerical variables not yet initialized. This is used on @jed's JSONP implementation.

<pre>
i=i||0;i++ // before
i=-~i      // after
</pre>

It can also be used to decrement a variable by flipping around the negation and complement:

<pre>
i=i||0;i-- // before
i=~-i      // after
</pre>

### Use ^ to check if numbers are not equal

<pre>
if(a!=123) // before
if(a^123) // after
</pre>

### Use number base for character to number conversion

`parseInt(n, 36)` is not only a very small character to number conversion, it also has the added value of being case-insensitive, which may save a .toLowerCase(), like in subzey's parseRoman function.

### Use current date to generate random integers

As seen in aemkei\'s Tetris game.

<pre>
i=0|Math.random()*100 // before
i=new Date%100 // after
</pre>

_Note: Do not use in fast loops, because the milliseconds might not change!_

## Strings
---

### Prefer `slice` over `substr` over `substring`

Prefer `slice(start,stop)` over `substr(start,length)` over `substring(start,stop)`. Omit the second parameter to fetch everything to the end of the string. Do not use negative positions. It may be shorter (e.g. s.substr(-n) fetches the last n characters) but does not work in Internet Explorer (including version 9).

### Split using ''

Use `s.split('')` to create a character array from a string. Unfortunately you can not use s[i] to access the characters in the string. This does not work in Internet Explorer (including version 9).

### Split using 0

Save two bytes by using a number as a delimiter in a string to be split, as seen in @jed's timeAgo function.

<pre>
"alpha,bravo,charlie".split(",") // before
"alpha0bravo0charlie".split(0)   // after
</pre>

### Use the little-known .link method

Strings have a built-in .link method that creates an HTML link. This is used in @jed's linkify function.

<pre>
html="<a href='"+url+"'>"+text+"</a>" // before
html=text.link(url)                   // after
</pre>

Strings also have several other methods related to HTML, as documented here.

### Use `.search` instead of `.indexOf`

First, because this RegExp implicit is 1 byte shorter, but you get the added value of coercion of undefined to /undefined/ instead of '' being matched at position zero. This is used in @atk's base64decoder function.

Warning: This will fail when you search with an invalid regular expression. For example, '.' as /./ matches any character, '+' as /+/ gives an error so you'd want to ensure you know what the value is.

### Use `.replace` or `.exec` for powerful string iteration

Since the .replace method can take a function as its second argument, it can handle a lot of iteration bookkeeping for you. You can see this exploited in @jed's templates and UUID functions.

### Use `Array` to repeat a string

<pre>
for(a="",i=32;i--;)a+=0 // before
a=Array(33).join(0)     // after
a="0".repeat(32)        // ES6
</pre>

## Conditionals
---

### Use `&&` and `||` where possible

<pre>
if(a)if(b)return c // before
return a&&b&&c     // after

if(!a)a=Infinity // before
a=a||Infinity    // after
</pre>

### Coercion to test for types

Instead of using typeof `x=='string'`, you can use `''+x===x`.

Instead of using typeof `x=='number'`, you can use `+x===x`. `+x` will coerce x to a number or NaN, so if it is anything else but a number, this will turn false. Warning: If someone goes really crazy on the prototypes, this will probably fail.

Instead of using typeof `x=='function'`, you can use `/^f/.test(typeof x)` as in @tkissing's template engine.

### Type-specific methods to test for types

Another way to test types is to check if type-specific methods are available. (Seen on @adius DOMinate)

Test the variable x with the shortest type specific method:

| Type | Test |
| String | x.big |
| Number | x.toFixed |
|Array | x.pop (x.map works on fewer browsers) |
| Function | x.call |
| textNode | x.data |

This technique is even faster than string comparison!

**Warning:** This will lead to wrong results if properties or methods with those names were added.

## Arrays
---

### Use elision

Array elision can be used in some cases to save bytes. See @jed's router API for a real-world example.
<pre>
[undefined,undefined,2] // before
[,,2]                   // after

// Note: Be mindful of elided elements at the end of the element list
[2,undefined,undefined] // before length is 3
[2,,]                   // after length is 2
</pre>

You may notice that the undefined turns empty. In fact, when we coerce an array into a string, the undefined turns to empty string. See one exploitation from @aemkei's Digital Segment Display

<pre>
b="";b+=x // before
b=[b]+x   // after
// Bonus: b=x+[b] uses same bytes as b=[b]+x, while b="";b=x+b uses one more byte over b="";b+=x.
Another exploitation is also useful:

((b=[1,2][a])?b:'') // before
[[1,2][a]]          // after
</pre>

### Use coercion to do .join(',')

You can use `''+array` instead of `array.join(',')` since the default separator of arrays is `","`.

Warning: this will only work if the contents of the Array are true-ish (except false) and consist of Strings (will not be quoted!), Numbers or Booleans, Objects and Arrays within arrays may lead to unwanted results:
<pre>
''+[1,true,false,{x:1},0,'',2,['test',2]]
// "1,true,false,[object Object],0,,2,test,2"
</pre>

### String coercion with array literal []
<pre>
''+1e3+3e7 // before
[1e3]+3e7  // after
</pre>

See @jed's [UUID](https://gist.github.com/982883) function.

### Use coercion to build strings with commas in them

<pre>
"rgb("+(x+8)+","+(y-20)+","+z+")"; // before
"rgb("+[x+8,y-20,z]+")";           // after
</pre>

Or if the first or last values are static:

<pre>
"rgb(255,"+(y-20)+",0)"; // before
"rgb(255,"+[y-20,"0)"];  // after
</pre>

### Use Arrays as Objects

When you need to return an Object, re-use an already declared Array to store properties. An Array is of type 'object', after all. Make sure the field names don't collide with any of Array's intrinsic properties.

### Test if Array has Several Elements

You can write if(array[1]) instead of if(array.length > 1).

Warning: This doesn't work when the item array[1] is falsy! So only use it when you can be sure that it's not. You can use if(1 in array) in that case.

## Regular Expressions
---

### Use shortcuts

`\d` is short for `[0-9]` and `\w` is short for `[A-Za-z0-9_]`. `\s` matches whitespace. Upper case shortcuts are inverted, e.g. `\D` matches non-digits. You can use these shortcuts inside character classes, e.g. `[\dA-F]` matches hex characters.

`\b` does not match a character but a word boundary where a word and a non-word character met (or vice versa). \B matches everywhere except at word boundaries. Some other shortcuts do not work, e.g. \Q...\E. For a full list check the ECMA column in the Regular Expression Flavor Comparison.

`/a|b/` is the same as `/(a|b)/`.

Sometimes it's shorter to use `<.*?>` (ungreedy matching) instead of `<[^>]*>` to match (for example) an HTML tag. But this may also change the runtime and behavior of the regular expression in rare cases.

In the replacement string, $& refers to the entire match and `$`` and `$'` refer to everything before and after the match, so `/(x)/,'$1'` can be replaced with `/x/,'$&'`.

### Denormalize to shorten

While `/\d{2}/` looks smarter, `/\d\d/` is shorter.

### Don't escape

In many cases almost no escaping (with `\`) is needed even if you are using characters that have a meaning in regular expressions. For example, `[[\]-]` is a character class with the three characters `[, ]` (this needs to be escaped) and `-` (no need to escape this if it's the last character in the class).

### eval() for a regexp literal can be shorter than RegExp()

Prefer `/\d/g` over `new RegExp('\\d','g')` if possible. If you need to build a regular expression at runtime, consider using `eval()`.

<pre>
// we escape the first curly bracket so if `p` is a number it won't be
// interpreted as an invalid repetition operator.
r=new RegExp("\\\\{"+p+"}","g") // before
r=eval("/\\\\{"+p+"}/g")    // after
</pre>

### eval() around String.replace() instead of callback

If a callback is used to achieve a certain effect on the output, one can use replace to build the expression that achieves the same effect and evaluate it (the more complicated the matches are, the less this will help):

<pre>
x.replace(/./,function(c){m=m+c.charCodeAt(0)&255})  // before
eval(x.replace(/./,'m=m+"$&".charCodeAt(0)&255;'))   // after
</pre>

## Booleans
---

### Use ! to create booleans

true and false can be created by combining the ! operator with numbers.
<pre>
[true,false] // before
[!0,!1]      // after
</pre>

Boolean coercion can be useful, too. If coerced to Number (e.g. by prefixing a +), true will coerce to 1, false to 0. So a program that will test one condition to output 2 and another one to output 1 and 0 if none is met, can be reduced:

<pre>
x>7?2:x>4?1:0 // before
+(x>7)+(x>4)  // after
</pre>

One way that minifiers are able to shave bytes off of JavaScript code is changing the way booleans are used, from David Walsh blog:
<pre>
true === !0 // before, save 2 chars
false === !1 // after, save 3 chars
</pre>

## Functions
---

### Shorten function names

Assign prototype functions to short variables. This may also be faster in more complex cases.

<pre>
a=Math.random(),b=Math.random() // before
r=Math.random;a=r(),b=r()       // after
</pre>

### Use named functions for recursion

Recursion is often more terse than looping, because it offloads bookkeeping to the stack. This is used in @jed's walk function.

### Use named functions for saving state

If state needs to be saved between function calls, name the function and use it as a container. This is used for a counter in @jed's JSONP function.
<pre>
function(i){return function(){console.log("called "+(++i)+" times")}}(0) // before
(function a(){console.log("called "+(a.i=-~a.i)+" times")})           // after
0,function a(){console.log("called "+(a.i=-~a.i)+" times")}           // another alternative
</pre>

### Omit () on new calls w/o arguments

new Object is equivalent to new Object()
<pre>
now = +new Date() // before
now = +new Date   // after
</pre>

### Omit the new keyword when possible

Some constructors don't actually require the new keyword.

<pre>
r=new RegExp(".",g) // before
r=RegExp(".",g)     // after

l=new Function("x","console.log(x)") // before
l=Function("x","console.log(x)")     // after
</pre>

### The return statement

When returning anything but an expression starting with an alphanumeric character, there’s no need to use a space after return:

<pre>
return ['foo',42,'bar']; // before
return['foo',42,'bar'];  // after
return {x:42,y:417}; // before
return{x:42,y:417};  // after
return .01; // before
return.01;  // after
</pre>

### Use the right closure for the job

If you need to execute a function instantly, use the most appropriate closure.

<pre>
;(function(){...})() // before
new function(){...}  // after, if you plan on returning an object and can use `this`
!function(){...}()   // after, if you don't need to return anything
</pre>

### Shorten function with Function

This is most useful with multiple function calls. However, both parameters must be strings, which may be a good or bad thing, so use this wisely.

<pre>
function a(a){return a}
function b(b){return b}
function c(c){return c} //before

var f=Function,
a=f('a','return a'),
b=f('b','return b'),
c=f('c','return c') //after
</pre>

## In the browser
---

### Use browser objects to avoid logic

Instead of writing your own logic, you can use browser anchor elements to parse URLs as in @jed's parseURL, and text nodes to escape HTML as in @eligrey's escapeHTML.

### Use global scope

Since window is the global object in a browser, you can directly reference any property of it. This is well known for things like document and location, but it's also useful for other properties like innerWidth, as shown in @bmiedlar's screensaver.

### A better way to getElementById()

Saves 27 bytes.

<pre>
document.getElementById('a').innerHTML = "foo"; // before
a.innerHTML = "foo"; // after
</pre>

### Delimiters

Only use ; where necessary. Encapsulate in other statements if possible, e.g.
<pre>
x=this;a=[].slice.call(arguments,1); // before
a=[x=this].slice.call(arguments,1);  // after
</pre>

## APIs
---

### Pass static data via argument where possible

### Use extra bytes to provide default values

### Do one thing and do it well

## Other resources

+ Ben Alman's explanation of his JS1K entry
+ Marijn Haverbeke's explanation of his JS1K entry
+ Martin Kleppe's presentation about his 140byt.es and JS1K entries
+ Suggested Closure Compiler optimizations
+ Angus Croll's blog
+ Aivo Paas's jscrush
+ Cody Brocious's post on superpacking JS demos


## References

+ [140bytes](https://github.com/jed/140bytes/wiki/Byte-saving-techniques)
