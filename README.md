# FitText.js, a <del>jQuery plugin</del> tiny script for inflating web type
FitText makes font-sizes flexible. Use this <del>plugin</del> script on your fluid or responsive layout to achieve scalable headlines that fill the width of a parent element.

[Demo on Github](http://atelierbram.github.io/FitText.js/)

## Attribution
- This javascript was originally a jQuery plugin by [Dave Rupert](https://github.com/davatron5000/FitText.js)
- This version [with vanilla javascript](http://jsbin.com/eberan/9/edit) by [Remy Sharp](http://remysharp.com/), he [writes about it here](http://remysharp.com/2013/04/19/i-know-jquery-now-what/)
- [Video of the talk](http://vimeo.com/68009123)

## How it works
If you have a small site and don't want to attach jQuery, just attach fittext-remy.js and put this just before `</body>` (fittext1 is an header id).

```html
<script>
  window.fitText(document.getElementById('fittext1'));
</script>
```

### From: [I know jQuery. Now what?](http://remysharp.com/2013/04/19/i-know-jquery-now-what/)

I was working on a project recently, and knew of the fitText.js project. I went to drop it in, but then spotted that it needed jQuery.

Hmm. Why?

It uses the following jQuery methods:

1. .extend
2. .each
3. .width
4. .css
5. .on (and not with much thought to performance)

The plugin is this:

```javascript
  $.fn.fitText = function( kompressor, options ) {

    // Setup options
    var compressor = kompressor || 1,
        settings = $.extend({
          'minFontSize' : Number.NEGATIVE_INFINITY,
          'maxFontSize' : Number.POSITIVE_INFINITY
        }, options);

    return this.each(function(){

      // Store the object
      var $this = $(this);

      // Resizer() resizes items based on the object width divided by the compressor * 10
      var resizer = function () {
        $this.css('font-size', Math.max(Math.min($this.width() / (compressor*10), parseFloat(settings.maxFontSize)), parseFloat(settings.minFontSize)));
      };

      // Call once to set.
      resizer();

      // Call on resize. Opera debounces their resize by default.
      $(window).on('resize orientationchange', resizer);

    });

  };
```

`.extend` is being used against an object that only has two options, so I would rewrite to read:

```javascript
  if (options === undefined) options = {};
  if (options.minFontSize === undefined) options.minFontSize = Number.NEGATIVE_INFINITY;
  if (options.maxFontSize === undefined) options.maxFontSize = Number.POSITIVE_INFINITY;

```

`return this.each` used to loop over the nodes. Let’s assume we want this code to work stand alone, then our `fitText` function would receive the list of nodes (since we wouldn’t be chaining):

```javascript
  var length = nodes.length,
      i = 0;

  // would like to use [].forEach.call, but no IE8 support
  for (; i < length; i++) {
    (function (node) {
      // where we used `this`, we now use `node`
      // ...
    })(nodes[i]);
  }
```

`$this.width()` gets the width of the container for the resizing of the text. So we need to get the computed styles and grab the width:

```javascript
  // Resizer() resizes items based on the object width divided by the compressor * 10
  var resizer = function () {
    var width = node.clientWidth;

    // ...
  };
```

It’s really important to note that swapping `.width()` for `.clientWidth` will not work in all plugins. It just happen to be the right swap for this particular problem that I was solving (which repeats my point: use the right tool for the job).

`$this.css` is used as a setter, so that’s just a case of setting the style:

```javascript
  node.style.fontSize = Math.max(...);
```

`$(window).on('resize', resizer)` is a case of attaching the event handler (note that you’d want `addEvent` too for IE8 support):

```javascript
  window.addEventListener('resize', resizer, false);
```

In fact, I’d go [further](http://jsbin.com/eberan/9/edit) and store the resizers in an array, and on resize, loop through the array executing the resizer functions.

Sure, it’s a little more work, but it would also easy to upgrade this change to patch in jQuery plugin support as an upgrade, rather than making it a prerequisite.

Rant will be soon over: it also irks me when I see a polyfill requires jQuery – but I know the counter argument to that is that jQuery has extremely high penetration so it’s possibly able to justify the dependency.

> taken from [remysharp.com/2013/04/19/i-know-jquery-now-what/](http://remysharp.com/2013/04/19/i-know-jquery-now-what/)

### Thanks
- Thanks to Trent, Dave and Reagan for [original FitText.js](https://github.com/davatron5000/FitText.js)
http://fittextjs.com/
- Thanks to [Remy Sharp](http://remysharp.com/2013/04/19/i-know-jquery-now-what/) for the [javascript used](http://jsbin.com/eberan/9/edit) in the [demo here on Github](http://atelierbram.github.io/FitText.js/)
- Thanks to [Jeremy Keith](http://adactio.com/) for the convenient [repo this was cloned from](https://github.com/adactio/FitText.js), one can see [his example demo here](http://atelierbram.github.io/FitText.js/example.html)

### Addition resources

- [Slides](https://speakerdeck.com/rem/i-know-jquery-now-what)
- [Video of the talk](http://vimeo.com/68009123)
- [Q & A session with Tim Kadlec from Mobilism](http://vimeo.com/68910118#t=2380)
- [min.js library](https://github.com/remy/min.js)

