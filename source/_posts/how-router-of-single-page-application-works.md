title: How router of Single Page Application works
date: 2015-02-08 17:04:39
tags:
- spa
- router
- pushstate
- js

---

In some javascript frameworks ([Backbone](http://backbonejs.org/), [Ember](http://emberjs.com/), [Angular](https://angularjs.org/), etc.), there is `Router`, which provide us ability to manage states in our applications.

<!-- more -->

Generally, there are two ways to manage url. One is **hash**, which relies upon the `hashchange` event existing in the browser. The other is **history**, which relies upon the browser's `history` API.

------

To bootstrap router, we should have some config first.

In Backbone,

```javascript
// use hash fragment
Backbone.history.start();
// use History API
Backbone.history.start({pushState: true})
```

In Ember,
```javascript
// use hash fragment
App.Router.reopen({location: 'hash'});
// use History API
App.Router.reopen({location: 'history'});
// automatic detection
App.Router.reopen({location: 'auto'});
```

In Angular,
```javascript
// use hash fragment
$locationProvider.html5Mode(false);
// use History API
$locationProvider.html5Mode(true);
```

------

As we know, when we use `hash`, the url behind `#` will not be sent to server. So we can simply add `hashchange` event to listen url change (in essence, it's hash change).
```javascript
window.onhashchange = function () {
  if (location.hash == '#about') {
    // do stuff here
  }
}
```

These frameworks have already wraped this default configuration for us, we just follow their own router way to config.

------

But for `history`, how it works? It relies upon `pushState` and `replaceState`.

When we click `<a>` tag to another link, we should do some works,
* prevent default behaviour
* get the url which we want to link to
* use pushState or replaceState to change the url without any refresh

For Ember and Angular, this is built-in support. So we should implement this by ourself in Backbone application.
```javascript
$(document).on("a", "click", function(e) {
  var href = $(this).attr("href");
  var protocol = this.protocol + "//";
 
  if (href.slice(protocol.length) !== protocol) {
    e.preventDefault();
    Backbone.history.navigate(href, true);
  }
});
```

------

One thing to **NOTE** is that, if we use `history` API, it's must be supported in our server side. Otherwise, we will get 404 page when we refresh.
