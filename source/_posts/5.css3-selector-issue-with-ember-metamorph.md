title: CSS3 selector issue with Ember metamorph
date: 2015-02-06 22:28:56
tags:
- ember
- css

---

[Ember.js](http://emberjs.com/) has been used in our application, and this week, I picked up a task to render a list, which seems very simple.

<!-- more -->
```html
<div class="settings-section">
  {{each organization in organizations}}
  <div class="settings-section__organization">
  ...
  </div>
  {{/each}}
</div>
```

As you know, Ember use [Handlebars](http://handlebarsjs.com/) to render. For visual design, the first item has no `margin-top`, and the last item has no `border-bottom`.

```css
.settings-section__organization {
  margin-top: 10px;
  border-bottom: 1px solid grey;
  &:first-child {
    margin-top: 0;
  }
  &:last-child {
    border-bottom: none;
  }
}
```
However, it **DOESN'T** work.
```html
<div class="settings-section">
  <script id="metamorph-23-start" type="text/x-placeholder">
  <script id="metamorph-19-start" type="text/x-placeholder">
  <div class="settings-section__organization">
  ...
  </div>
  <script id="metamorph-16-start" type="text/x-placeholder">
  <script id="metamorph-23-start" type="text/x-placeholder">
</div>
```
As the rendered template, Handlebars inserted marker elements into DOM, as known as `metamorph`.
> Tips: *If you want to avoid your property output getting wrapped in these markers, you can use the `unbound` helper, which will make the output won't be automatically updated.*

So both first element and last element are not matched as expected.

Then I want to use `:first-of-type` and `:last-of-type`.
```css
.settings-section__organization {
  margin-top: 10px;
  border-bottom: 1px solid grey;
  &:first-of-type {
    margin-top: 0;
  }
  &:first-of-type {
    border-bottom: none;
  }
}
```
Actually this reminds me that, these two selectors are made for `elements`, such as `div:first-of-type`. It may doesn't work for css class.

In fact, it **WORKS**.

After investigation, I found `.class:first-of-type` is not perfect for this issue, because it means:
>*Select an element if it has the given class and is the first of its type among its siblings.*

In other words, if we add a `<p>` tag before `<div class="settings-section__organization">`, it works. But if we add a `<div>` tag with no class, it breaks down.

I share this interesting issue to other F2E guys today, and one of them give me a css trick.
```css
.settings-section__organization {
  margin-top: 0;
}

.settings-section__organization ~ .settings-section__organization {
  margin-top: 10px;
}
```
The above solution will apply a top margin of 10px on **ONLY** the first `.settings-section__organization` class, which benefited from the general sibling selector, **tilde sign** `~`. It's definitely a hack but it works great on most browsers.

In addition, this doesn't fix the issue of needing to select the last child obviously, which have yet to find a pure CSS solution for.

One thing to keep in mind is that from Ember 1.8.0, there is a change log,
> *Remove metamorph in favor of morph package (removes the need for script tags in the DOM).*

Which means we will finally get rid of all those metamorph tags in the DOM. At that time, we can just remove the CSS hacks required to do child-based styling in favor of using native CSS selectors and pseudo selectors.

### Updated few days later

However, there is another approach which we can bypass the last-child issue.
```css
.settings-section__organization {
  margin-top: 0;
}

.settings-section__organization ~ .settings-section__organization {
  margin-top: 10px;
  border-top: 1px solid grey;
}
```
As above, we can simply add `border-top` to each item except the first one, instead of adding `border-bottom` to each item except the last one.

Does it also likes a hack, huh?
