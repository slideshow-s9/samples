%%%%%%%%%%%%%%%%%%
%% Some Headers

Title: Using CSS3 Gradients for Backgrounds in Your Slides

%%%%%%%%%%%%%%%%%%%%%%%%%
%% Some Inline Ruby Helpers

%helper
  def background_demo( code )
   
    # add background style rule also to source as code block for demo
    # (for built-in background helper see:
    #   http://github.com/geraldb/slideshow/tree/master/lib/slideshow/helpers)
   
    buf  = background( code )  
    buf << "\n    #{code}\n"
    buf
  end
%end

%%%%%%%%%%%%%%%%%%%%%%%%%
%% Some Extra CSS 

%css 
  
h1, h2  { text-align: center; }

pre {
  padding: 4px;
  border-top: #bbb 1px solid;
  border-bottom: #bbb 1px solid;
  background: #f3f3f3;
}
    
%end

%%%%%%%%%%%%%%
%% Let's go.

# Understanding Gradients

(Adapted S6/S9 Version <sup>1</sup> from [Original Article](https://developer.mozilla.org/en/Using_gradients))
%class center

Firefox (3.6+) includes support for gradients in the CSS background.
Using CSS gradients in a background lets you display smooth transitions
between two or more specified colors.

Firefox supports two types of gradients:

* linear (`-moz-linear-gradient`)
* radial (`-moz-radial-gradient`)

<%= background '-moz-linear-gradient(top, silver, white, silver, white)', :class => 'cover' %>

---
<sup>1</sup> {{ source path=3rd }}

{{ help }}


# Simple Linear Gradients

Here's a linear gradient that starts at the center (horizontally)
and top (vertically), and starts blue, transitioning to white:

<%= background_demo '-moz-linear-gradient(top, blue, white)' %>


# Left to Right

Changing the same gradient to run from left to right:

<%= background_demo '-moz-linear-gradient(left, blue, white)' %>


# Run Diagonally 

You can make the gradient run diagonally by specifying both
the horizontal and vertical starting positions:

<%=  background_demo '-moz-linear-gradient(left top, blue, white)' %>


# Using Angles

If you don't specify an angle,
one is determined automatically based on the start position.
If you'd like more control over the direction of the gradient,
you can set the angle specifically.

For example, here are two gradients that have the same starting point
of `left center`,  ...

<%= background_demo '-moz-linear-gradient(left, orange, white)' %>

# Using Angles (Cont'd)

...but the second one also has an angle of 20 degrees.

<%= background_demo '-moz-linear-gradient(left 20deg, orange, white)' %>  

# `0deg`

The angle is specified as an angle between a horizontal line
and the gradient line, going counter-clockwise.
In other words, `0deg` generates a left to right horizontal gradient, ...

<%= background_demo '-moz-linear-gradient(0deg, red, white)' %>


# `90deg`

...while `90deg` creates a vertical gradient from the bottom to the top:

<%= background_demo '-moz-linear-gradient(90deg, red, white)' %>

# `180deg`

<%= background_demo '-moz-linear-gradient(180deg, red, white)' %>

# `-90deg`

<%= background_demo '-moz-linear-gradient(-90deg, red, white)' %>


# Color Stops

Color stops are points along the gradient line that will have a specific color
at that location. The location can be specified as either a percentage
of the length of the line, or as an absolute length.
You may specify as many color stops as you like
in order to achieve the desired effect.

If you specify the location as a percentage, 0% represents the starting point,
while 100% represents the ending point; however, you can use values outside
that range if necessary to get the effect you want.

This example specifies three color stops:

<%= background_demo '-moz-linear-gradient(top, blue, white 80%, orange)' %>

Note that the first and last color stops don't specify a location;
because of that, values of 0% and 100% are assigned automatically.
The middle color stop specifies a location of 80%,
putting it most of the way toward the bottom.


# Evenly Spaced Color Stops

Here's an example using a wide variety of colors, all evenly spaced:

<%= background_demo '-moz-linear-gradient(left, red, orange, yellow, green, blue)' %>  

Notice that the color stops are automatically spaced evenly when no locations are specified.


# Radial Gradients

Radial gradients are specified using the `-moz-radial-gradient` property.
The syntax is similar to that for linear gradients,
except you can specify the gradient's shape (whether it should be a circle or ellipse)
as well as its size.

You specify color stops the same way as for linear gradients.
The gradient line extends out from the starting position in all directions.

<%= background_demo '-moz-radial-gradient(red, yellow, rgb(30, 144, 255))' %> 

By default, as with linear gradients, the color stops are evenly spaced:


# Explicitly Spaced Color Stops

Here we specify specific locations for the color stops:

<%= background_demo '-moz-radial-gradient(red 5%, yellow 25%, #1E90FF 50%)' %>  


# Size  --  `closest-side` for Ellipses

This is one of the areas in which radial gradients differ from linear gradients.
You can provide a size value that specifies the point that defines
the size of the circle or ellipse. See this description of the size constants for specifics.

This ellipse uses the `closest-side` size value,
which means the size is set by the distance from the starting point
(the center) to the closest side of the enclosing box.

<%= background_demo '-moz-radial-gradient(ellipse closest-side, red, yellow 10%, #1E90FF 50%, white)' %>


# `farthest-corner` for Ellipses

This example is similar to the previous one,
except that its size is specified as `farthest-corner`,
which sets the size of the gradient by the distance from the starting point
to the farthest corner of the enclosing box from the starting point.

<%= background_demo '-moz-radial-gradient(ellipse farthest-corner, red, yellow 10%, #1E90FF 50%, white)' %>


# `closest-side` for Circles

This example uses `closest-side`, which determines the circle's size
as the distance between the start point (the center) and the closest side.

<%= background_demo '-moz-radial-gradient(circle closest-side, red, yellow 10%, #1E90FF 50%, white)' %>

Here, the circle's radius is half the height of the box,
since the top and bottom edges are equidistant from the start point
and are closer than the left and right edges.


# Repeating Gradients

The `-moz-linear-gradient` and `-moz-radial-gradient`  properties
don't support automatically repeating the color stops.
However, the `-moz-repeating-linear-gradient`  and `-moz-repeating-radial-gradient`
properties are available to offer this functionality.

This example uses `-moz-repeating-linear-gradient` to create a gradient:

<%= background_demo '-moz-repeating-linear-gradient(top left -45deg, yellow, yellow 5px, white 5px, white 10px)' %>


# Repeating Radial Gradient

This example uses `-moz-repeating-radial-gradient` to create a gradient:

<%= background_demo '-moz-repeating-radial-gradient(orange, orange 5px, white 5px, white 10px);' %>


# More Links, Resources -- The End

Mozilla

* [Using Gradients](https://developer.mozilla.org/en/Using_gradients)

Apple

* [Safari Visual Effects Guide -- Gradients](http://developer.apple.com/safari/library/documentation/InternetWeb/Conceptual/SafariVisualEffectsProgGuide/Gradients/Gradients.html)

Gradient Generators

* [CSS3 Gradient Generator](http://gradients.glrzad.com/) by Damian Galarza
* [Linear Gradients Generator](http://www.westciv.com/tools/gradients/) by John Allsopp
* [Radial Gradients Generator](http://www.westciv.com/tools/radialgradients/) by John Allsopp

<%= background '-moz-linear-gradient(top, yellow, orange, yellow, orange)' %>
