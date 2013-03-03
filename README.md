# intention.js

DOM Manipulation based on a html attribute specification

## Why Intention.js

The interface to define differences between documents should be in HTML. The manipulation of attributes is a better way to restructure a page than media queries. Because relying on CSS/HTML document flow patterns to change the hierarchy of a design is not sufficient to convey appropriate infomation. 

## Installation

include the script on your page via require
```html
	<!-- use with context defaults -->
	<script data-main="assets/js/context" src="assets/js/require/require.js"></script>
	<!-- OR -->
	<!-- use only intention to build your own context -->
	<script src="intention.js"></script>
	<script>
		// your amazing contextual threshold specification here!
	</script>
```


## Usage

By default context.js provides a number of threshold groups via intention.js: browser width, touch/nontouch, highrez/normal, and a default group

the default thresholds in each group are respectively: 
mobile (320 and below), tablet (321 to 768) and standard (769 to Infinity)
touch (are touch gestures available)
highrez (devicePixelRatio > 1)
base (default, always on)

Three manipulation types: class, attr, placement

### Interface

```html
	<!-- flag the element as responsive -->
	<div tn>
	<!-- or -->
	<div data-tn>
	<!-- For the purposes of the documentation I will always use the prefix "tn-" instead of "data-tn-" to keep it short -->
	<!-- attribute structure: prefix-context-function ie tn-mobile-class OR tn-highrez-source -->
	<div class="not interesting" tn tn-mobile-class="more interesting">
```

#### Attribute Manipulation
```html
	<!-- mark an element as responsive, set the base(default) attr, specify which image to load in a given context -->
	<img
		tn 
		tn-base-src="small_img.png" 
		tn-highrez-src="big_img.png" />
	<!-- the above spec will produce the following in each context
		default: <img src="small_img.png" />
		highrez: <img src="big_img.png" />
	-->
```

#### Class Manipulation

An element can have more than one class. tn's aim is to be as unobtrusive as possible, at the same time allowing for a lot of flexibility with the way classes are combined.

```html
	<section
		class="column"
		tn-mobile-class="narrow"
		tn-tablet-class="medium"
		tn-standard-class="wide"
		tn-luxury-class="x-wide"
		tn-touch-class="swipe-nav"
	>...</section>
	<!--  -->
```

#### Placement Manipulation

take this html structure

```html
	<header><nav></nav></header>
	<section>...</section>
	<footer>...</footer>
```

suppose we want to demote the status of the nav when the user is on smaller devices

the following specification on the nav might do what we need

```html
	<nav tn 
		tn-mobile-prepend="footer"
		tn-tablet-append="section"
		tn-standard-append="header">
```

when the device is 320px units or below the nav will appear at the top of the footer. when the device is between 320 and 768 it will go to the end of the section tag, and so forth.

##### Move functions
	* prepend
	* append
	* before
	* after

#### Why a base context?

In most scenarios you don't want to have to specify the way something will change in *every* context. Often times an element will be one of two things among many different contexts. take an img tag with two possible sources, it's either going to be highrez or not. by specifying the tn-highrez-src attribute, you know that the source will be appropriately applied in that scenario. With a tn-base-src attribute, you can rely on the source being set accordingly for all other contexts.

### Custom Thresholds

In addition to what is provided as a set of useful page contexts in the context.js script. You can define your own contexts, for anything!

Take this example for scroll depth:

```javascript
	var responsiveDepths = tn.responsive(
		// contexts
		[{name:'shallow', value:20}, {name:'deep', value:1/0}],
		// matching:
		function(measure, context){
			if(measure < context.value) return true;
		},
		// measure
		function(){
			return window.pageYOffset;
		});
	$(window).on('scroll', responsiveDepths);
```

#### The components tn.responsive

##### Thresholds

The thresholds are an array of context objects. the only requirement of these objects is that they have a name property. Specifying any other property is up to you.

```javascript 
	// required
	[{name:'shallow'}, {name:'deep'}]
	// or with a little extra
	[{name:'shallow', value:20}, {name:'deep', value: Infinity}]
```

##### Matching function

The matching function is called for each item in the thresholds array until a match is made i.e. it returns true.

The context that produces a match is then understood as the current context for the threshold group.

If a matching function is not specified this default is used:
```javascript
    function(measure, context){
      if(measure===context.name) return true;
      return false;
    };
```

##### Measure function

default measure function is a pass-through

```javascript
	function(arg){
      return arg;
    };
```

why?

tn.responsive() // outputs a function

so calling the result of that function with an argument passed to it will get used as the measure arg in the *matcher* function

like so:

```javascript
	// make a responsive group *thresholds* is the array of contexts and *matcher* is a custom comparison function
	var responsive = tn.responsive(thresholds, matcher);
	// assuming we want to compare the scroll depth against each context you could do something like this:
	responsive(window.pageYOffset);
```

in this example window.pageYOffset would get passed as the first argument to the matcher function for every context until the matcher returns true.

#### Putting it all together

Threshold objects must be passed to tn.responsive as an array

The only other requirement is that the threshold object has a "name" property, i.e. {name:'bruce'}. The name is used for two main things: emmiting an event of that name on the tn object and allowing you to create specifications in the html for that threshold.

to create an event handler for a threshold:

```javascript
tn.on('bruce', function(){
	alert('bruce? what are you doing here?');
});
```

to specify changes to the html when in that threshold

```html
	<img tn tn-bruce-src="bruce_willis.gif" />
```

#### Default Compare Functions

```javascript
	// matching default		      
    matcher = function(measure, ctx){
      if(measure===ctx.name) return true;
      return false;
    };
  	// measure default is just a pass through
    measure = function(arg){
      return arg;
    };
```

#### About Matching

top down search, ends when a match is found
in other words just one match per context group

### Stuff to note:


### Master list of functions:

#### Multi-value attr (union of all current contexts)
	* class
#### Move Functions
	* append
	* prepend
	* before
	* after
#### Single-value attrs, (everything else)
	* src
	* href
	* height
	* width
	* title
	* tabindex
	* id
	* style
	* align
	* dir
	* contenteditable
	* lang
	* xml:lang
	* accesskey
	* background
	* bgcolor
	* contextmenu
	* draggable
	* hidden
	* item
	* itemprop
	* spellcheck
	* subject
	* valign


## Authors

	* Joe Kendall
	* Erin Sparling


## License

// MIT licesnse for everything

// Copyright (c) 2012 The Wall Street Journal, 
// http://wsj.com/

// Permission is hereby granted, free of charge, to any person obtaining
// a copy of this software and associated documentation files (the
// "Software"), to deal in the Software without restriction, including
// without limitation the rights to use, copy, modify, merge, publish,
// distribute, sublicense, and/or sell copies of the Software, and to
// permit persons to whom the Software is furnished to do so, subject to
// the following conditions:

// The above copyright notice and this permission notice shall be
// included in all copies or substantial portions of the Software.

// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
// EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
// MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
// NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
// LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
// OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
// WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.