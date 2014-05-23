# Brightspot JavaScript libraries

### [brightspot-js-autoexpand](https://github.com/perfectsense/brightspot-js-autoexpand)

`input[type="text"]` and `textarea` that automatically expands to fit their content.

### [brightspot-js-autosubmit](https://github.com/perfectsense/brightspot-js-autosubmit)

Form inputs that automatically submit the form on `change` or `input` events.

### [brightspot-js-grunt](https://github.com/perfectsense/brightspot-js-grunt)

Standard Grunt configuration for Brightspot projects.

### [brightspot-js-select](https://github.com/perfectsense/brightspot-js-select)

`select` element replacement.

### [brightspot-js-share](https://github.com/perfectsense/brightspot-js-share)

Social sharing bar.

### [brightspot-js-utils](https://github.com/perfectsense/brightspot-js-utils)

Miscellaneous utility functions.

# External Libraries

### [Snap.js](https://github.com/jakiestfu/Snap.js/)

Mobile slide menu.

### [truncate.js](https://github.com/gitastrophe/truncate.js/)

a jQuery plugin for deep-HTML truncation and toggling

### [lazyload.js](https://github.com/gitastrophe/lazyload.js/)

a jQuery plugin for lazy-loading images, then getting on with your code

### [MediaClass](https://github.com/gitastrophe/MediaClass)

a JavaScript implementation of [element queries](http://www.jonathantneal.com/blog/thoughts-on-media-queries-for-elements/) by Jonathan Neal, forked with modifications for optimized triggering

# Useful Snippets

### Centering an element in a page

    jQuery.fn.center = function() {
       this.css("position","absolute");
       this.css("top", Math.max(0, (($(window).height() - this.outerHeight()) / 2) + 
           $(window).scrollTop()) + "px");
       this.css("left", Math.max(0, (($(window).width() - this.outerWidth()) / 2) + 
           $(window).scrollLeft()) + "px");
       return this;
    }
