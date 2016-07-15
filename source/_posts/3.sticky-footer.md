title: Sticky footer
date: 2014-12-21 22:03:02
tags:
- css

---

When I built up this blog via [hexo](https://github.com/hexojs/hexo) and themed it via [noderce](https://github.com/willerce/hexo-theme-noderce), I found there was a bug on UI -- The footer of the page wasn't stuck to the bottom because of the thin on content.

<!-- more -->

Expectantly, we want our footer will be stuck to the bottom even when thin on content.

After I googled, I found there was a site which named [cssstickyfooter](http://www.cssstickyfooter.com/) and supported cross browser code for sticky footer, you can found the code [here](http://www.cssstickyfooter.com/using-sticky-footer-code.html).

However, the code on the site above only apply to the specific structure:

```html
<div id="wrap">
  <div id="main">
  </div>
</div>
<div id="footer">
</div>
```
or
```html
<div id="wrap">
  <div id="header">
  </div>
  <div id="main">
  </div>
</div>
<div id="footer">
</div>
```

And the structure of this blog like below:

```html
<div id="header">
</div>
<div id="wrap">
  <div id="main">
  </div>
</div>
<div id="footer">
</div>
```

Which means if I want to use the solution given by [cssstickyfooter](http://www.cssstickyfooter.com/), I have to change my html structure or change a little bit code.

At last, I found a nice solution on [bootstrap](http://getbootstrap.com/examples/sticky-footer/), regardless of where the header is.

For example, we can just use few code below to implement sticky footer for this blog:

```css
html {
  position: relative;
  min-height: 100%;
}

#main {
  padding-bottom: 75px;
}

footer {
  position: absolute;
  bottom: 0;
  width: 100%;
}
```
It look likes cool now?
