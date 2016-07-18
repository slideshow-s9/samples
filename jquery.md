%%%%%%%%%%%%%%
% Some Headers

Title: Prototype &hArr; jQuery - To and from JavaScript libraries

%%%%%%%%%%%%%%%%%%%%
% Some Extra Styling

%css

table#ajax      { width: 100%; }

table#ajax td   { text-align: center; }

%end

%%%%%%%%%%%%%
% Slides Start Here

# Prototype &hArr; jQuery  

To and from JavaScript libraries 

[Remy Sharp](http://remysharp.com)

(Adapted S6 Version from Original PDF Slide Deck)



# Why Prototype?

* Extends the DOM and core JavaScript objects
* An arsenal of utility functions

Based on Prototype 1.5.1 & 1.6
{:.right .smaller}

# Why jQuery

* Centred around chaining and binding methods to objects and elements
* Totally encapsulated
* Aims to be exceptionally easy to develop with

Based on jQuery 1.2.1
{:.right .smaller}

# Differences in Native Support

{% left %}

Prototype has:

* Class creation
* Try.these
* Position, Range

{% end %}

{% right %}

jQuery has: 

* No Conflict (`$`) 
* Effects 

{% end %}

Non-exhaustive list, and in most case, the functionality can be implemented with plugins.
{: .smaller}


# Syntax Comparison

# Dollar Variable

* Prototype uses `$` for id based selection
* jQuery `$` = CSS based selector (= `$$` in Prototype)

Note that Prototype will return element objects or arrays of
elements for most methods. jQuery will usually return a jQuery
object (which looks like an array in Firebug).
{:.smaller}

# `$` Example

Prototype

```
$('speech1').show();
```

jQuery

```
$('#speech1').show();
```

# CSS Based Selectors

* Prototype - `$$`

To narrow down it's context use
`Element.getElementsBySelector(selector)`
(or `Element.select(selector)` in 1.6)

* jQuery - `$`

Virtually all of jQuery's DOM selection is done using CSS 1-3


# Selector Examples

Prototype

```
$$('.dialog').invoke('show');

$('final-speech').getElementsBySelector('DIV.final-dialog').each(Element.hide);

// 1.6
$('final-speech').select('DIV.final-dialog').invoke('hide');
```

jQuery

```
$('.dialog').show();

$('#final-speech DIV.final-dialog').hide();
```

# DOM Ready Event

* Prototype - uses Event object
* jQuery - uses two types of syntax, both meaning the same thing

jQuery uses different methods to execute the ready function when
the DOM is ready, using specific methods for Internet Explorer
and for Safari (1)
{:.smaller}

1: [`window.onload` again](http://dean.edwards.name/weblog/2006/06/again/), [The `window.onload` Problem Revisited](http://blog.outofhanwell.com/2006/06/08/the-windowonload-problem-revisited/)

# Ready Example

Prototype

```
Event.observe(window,'load',function(){});
```

Prototype 1.6

```
document.observe('contentloaded',function{});
```

jQuery

```
$(document).ready(function(){});
 // or
$(function(){});
```

# Iteration

Prototype - current active element, and
position is passed in to callback function.

```
[el1, el2].each(fn(el, i))
```

jQuery - current element position passed
in to callback function, and binds the
function to current active element (i.e. this
is set to the active element).

```
$([el1, el2]).each(fn(i))
```

# DOM Walking

Prototype - up, down, next & previous

jQuery - parent/s, children, next, prev (& nextAll, prevAll)


# DOM Manipulation

Prototype - Insertion class: After, Before, Bottom, Top, update (1.6 will add: `Element.insert`)

jQuery - after, before, append, prepend & html


# Element Classes

Prototype - `addClassName`, `removeClassName`, `toggleClassName`, `hasClassName`

jQuery - `addClass`, `removeClass`, `toggleClass`, is (for class matching)


# Events

Prototype - Event class: `observe`, `stopObserving`
(Prototype 1.6 will support `Element.observe`)

jQuery - `bind`, `unbind` (also supports shortcuts: `.click`, `.dblclick`, `.mouse*`, `.ready`,
`.focus`, `.blur`)


# Bubbling

Prototype - `Event.stop()`

jQuery - return false or `event.stopPropagation()` (event is passed in
to the callback)

# Ajax

Prototype

```
new Ajax.Request(url[, options])
```

jQuery

```
$.ajax(options) // url included in options
```

# Ajax - Method Comparison

| Prototype     |        | jQuery        |
|:--------------|:-------|:--------------|
| `onCreate`    | &hArr; | `beforeSend`  |
| `onSuccess`   | &hArr; | `success`     |
| `onException` | &hArr; | `error`       |
| `onComplete`  | &hArr; | `complete`    |
{:#ajax}

# Ajax Examples

Prototype

```
new Ajax.Request('/profile', {
  method: 'post',
  parameters:$H({'action':'check_username','username':$F('username')}),
  onSuccess: function (j) {
    // do stuff with response
  }
});
```

jQuery

```
$.ajax({ url: '/profile',
  data: {'action':'check_username','username': $('#username').val()},
  type: 'post',
  success: function (json) {
    // do stuff with response
  }
});
```

# Plugins / Extensions

Prototype

```
Element.addMethods({myPlugin : function(element, args) { return element; }});
```

jQuery

```
jQuery.fn.myPlugin = function (args) { return this; };
```

# Browser Detection

Prototype - `Prototype.Browser.IE`, `.Webkit`, etc.

jQuery - `jQuery.browser.msie`, `.safari`, etc.


# Resources

|           | Prototype   | jQuer  y  |
|:----------|:------------|:----------|
| API       | [`prototypejs.org/api`](http://prototypejs.org/api) | [`docs.jquery.com/Core`](http://docs.jquery.com/Core)  |
| Tutorials | [`prototypejs.org/learn`](http://prototypejs.org/learn) | [`docs.jquery.com/Tutorials`](http://docs.jquery.com/Tutorials)  |
| Effects   | [`script.aculo.us`](http://script.aculo.us) | [`docs.jquery.com/Effects`](http://docs.jquery.com/Effects)  |
{:#resources}



