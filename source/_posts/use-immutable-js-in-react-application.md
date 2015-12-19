title: "Use immutable.js in React Application"
date: 2015-07-18 10:38:24
tags:
- immutable
- flux

---

In [previous article](http://just4fun.github.io/hexo-blog/2015/05/12/flux-architecture/), we got a general understanding about Flux architecture. And in [Best Practise](http://just4fun.github.io/hexo-blog/2015/05/12/flux-architecture/#Best_Practise) section, I have mentioned `Immutable data` which I haven't take a look yet. Now it's time to dig into it.

<!-- more -->

## Preliminaries

If you have experience writing websites with React, you should know React makes use of a `virtual DOM`, which is a descriptor of a DOM subtree rendered in the browser. It is important to understand that the result of render is not an actual DOM node. Those are just lightweight JavaScript objects. That's virtual DOM.

When `setState` is called, the component rebuilds the virtual DOM for its children. If you call `setState` on the root element, then the entire React App is re-rendered. All the components, even if they didn’t change, will have their render method called. This may sound scary and inefficient but in practice, this works fine because we’re not touching the actual DOM.

DOM operations are very expensive because modifying the DOM will also apply and calculate CSS styles, layouts. The saved time from unnecessary DOM modification can be longer than the time spent diffing the virtual DOM. This is the secret of React's virtual DOM.

However, the idea of re-rendering an entire subtree of components in response to every state change makes people wonder whether this process negatively impacts performance. That being said, if you want to get a considerable performance boost, you can minimize the number of costly DOM operations required to update the UI.

## Solution

React provides a component lifecycle function, `shouldComponentUpdate`, which is triggered before the re-rendering process starts (virtual DOM comparison and possible eventual DOM reconciliation), giving the developer the ability to short circuit this process. The default implementation of this function returns `true`, leaving React to perform the update.
```javascript
shouldComponentUpdate: function(nextProps, nextState) {
  return true;
}
```

We could easily implement `shouldComponentUpdate` for simple props/state structures like bellow:
```javascript
shouldComponentUpdate: function(nextProps, nextState) {
  return this.props.value !== nextProps.value) 
      || this.state.value !== nextState.value);
}
```

We could even generalize an implementation based on shallow equality (cause React will invoke this function pretty **OFTEN**, so the implementation has to be **FAST**) and mix it into components. In fact, React already provides such implementation: [PureRenderMixin](https://facebook.github.io/react/docs/pure-render-mixin.html).

## Issue

So far so good. 
Unfortunately, there may be two issues if we bring `PureRenderMixin` into our App directly.

---
> *Manage object references carelessly*

I pushed some commits into the demo App mentioned in previous article. One of the commits is adding a functionality `Like`, you can click heart icon to like a book. See code [here](https://github.com/just4fun/classics/commit/c010dc4cbacc021c45594720955a73664b28deec).
<div class="illustration">
![](/hexo-blog/img/ibook-like-functionality.jpg)
</div>

In addition, I added `debug` module to track which components will be re-render. See code [here](https://github.com/just4fun/classics/commit/f2915ad12d644f8691ab6ee309b7e8ceb1c9aedf).

Now I search some books via entering keyword.
<div class="illustration">
![](/hexo-blog/img/re-render-monitor.jpg)
</div>

Then I want to like the first book, so I click the heart icon.
<div class="illustration">
![](/hexo-blog/img/re-render-monitor-like.jpg)
</div>

As you see, all the book items are re-rendered. Since we want to squeeze out performance, then I bring `PreRenderMixin`. See code [here](https://github.com/just4fun/classics/commit/6ff882cd9c4913d92d9c4b7dbfc5e447f1df598e).

Unfortunately, **NOTHING** happen, neither the like icon nor the Chrome console.

The problem is that since the parent and inner components share a reference to the same object `book`, when the object gets mutated on onClick function, the prop the inner component had will change. So, when the re-rendering process starts, and `shouldComponentUpdate` gets invoked, `this.props.book` will be equal to `nextProps.value.book`, because in fact, they reference the same object.
```javascript
var BookItem = React.createClass({
  
  mixins: [PureRenderMixin],
  
  render: function() {
    var book = this.props.book;
    // ...
    var like_status = 'glyphicon glyphicon-heart';
    if (book.isLike) {
      like_status += ' like';
    }

    debug('render <BookItem />', book.title);
    return (
      <div className='main-section__book'>
        // ...
        <div className='main-section__book-action'>
          <span className={like_status} onClick={this._onClick.bind(this, book)}></span>
        </div>
      </div>
    );
  },
  
  _onClick: function(book) {
    BookGetActions.like(book); // the `book` share the same reference with `this.props.book`
  }

});
```

To solve this issue, first of all, we should clone another book object to prevent the same reference sharing.
```javascript
_onClick: function(book) {
  BookGetActions.like(JSON.parse(JSON.stringify(book))); // a simple way to clone
}
```

Then we should find the specific book in book list which is stored in `BookStore`, and replace it with the clone one. Code is omitted.

Everything seems OK. However, it may bring another issue.   

---
> *complex data structures*

As [React doc](https://facebook.github.io/react/docs/pure-render-mixin.html) mentioned, `PureRenderMixin` only **SHALLOWLY** compares the objects. If these contain complex data structures, it may produce false-negatives for deeper differences, which will cause re-render even everything between two objects are same.

## Real solution

[Immutable-js](https://github.com/facebook/immutable-js) is a JavaScript collections library which provides immutable persistent collections. Immutability makes tracking changes cheap; a change will always result in a new object so we **ONLY** need to check if the reference to the object has changed.

Immutable data structures provides you a cheap and less verbose way to track changes on objects, which is all we need to implement `shouldComponentUpdate`. Therefore, if we model props and state attributes using the abstractions provided by immutable-js we'll be able to use `PureRenderMixin` and get a nice boost in perf. See code [here](https://github.com/just4fun/classics/commit/ce3474f2b6d40a6a5c6e1e1b3d56681f5ca2b95f).

Run our App again, and like the first book, then check the Chrome console.
<div class="illustration">
![](/hexo-blog/img/re-render-monitor-immutable-like.jpg)
</div>

As we expected, only the liked book has been re-rendered. That's what Immutable-js do for us.

## References

- https://facebook.github.io/react/docs/advanced-performance.html
- https://facebook.github.io/react/docs/pure-render-mixin.html
- http://calendar.perfplanet.com/2013/diff/
