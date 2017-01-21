title: Reloading in Ember findAll and findRecord
date: 2017-01-21 17:46:08
tags:
- ember

---

Recently, QA reported two issues that which seems a little weird. After some investigations, I found they're caused by my lack of knowledge of Ember Data APIs.

<!-- more -->

## Background

- **We always got old records in Ember Data even it seems we have already sent new request and got new records from API**

In our application, there is a dropdown list at the top of screen which contains many `branches`. Once we select a new branch, we will send a request.

```javascript
didReceiveAttrs() {
  this._super(...arguments);
  this.setupFilterGroups();
},

setupFilterGroups() {
  // ...

  get(this, 'store').findAll('zipcodeFilter').then(zipcodes => {
    // we always got old `zipcodes` for last branch even we switched to a new branch
  });
}
```

After I dug into [documentation](http://emberjs.com/api/data/classes/DS.Store.html#method_findAll) for `findAll`,

> `findAll` asks the adapter's `findAll` method to find the records for the given type, and returns a promise which will resolve with all records of this type present in the store, even if the adapter only returns a subset of them.

In addition,

> When the returned promise resolves depends on the reload behavior, configured via the passed `options` hash and the result of the adapter's `shouldReloadAll` method.

There is also an example,

> If `{ reload: true }` is passed or `adapter.shouldReloadAll` evaluates to `true`, then the returned promise resolves once the adapter returns data, regardless if there are already records in the store:

```javascript
store.push({
  data: {
    id: 'first',
    type: 'author'
  }
});

// adapter#findAll resolves with
// [
//   {
//     id: 'second',
//     type: 'author'
//   }
// ]

store.findAll('author', { reload: true }).then(function(authors) {
  authors.getEach("id"); // ['first', 'second']
});
```

> If no reload is indicated via the abovementioned ways, then the promise immediately resolves with all the records currently loaded in the store.

That being said, without `reload: true`, `findAll` will always be resolved immediately if there are already records in the store, which will cause current issue.

```javascript
didReceiveAttrs() {
  this._super(...arguments);
  this.setupFilterGroups();
},

setupFilterGroups() {
  // ...

  get(this, 'store').findAll('zipcodeFilter', { reload: true }).then(zipcodes => {
    // it WORKS now, `zipcodes` will always be new records from API
  });
}
```

- **A button will always be enabled before all the requests it depends on resolved**

There is a button in our application, we introduced [`ember-concurrency`](http://ember-concurrency.com/) and bound `disabled=isProcessing` to it with three `task`s.

```javascript
createFollowup: task(function *(followupStatus, followupDate) {
  // ...
}),

clearFollowup: task(function *() {
  // ...
}),

createContactAttempt: task(function *(subject, notes, contactStatus) {
  // ...

  yield this.store.findRecord('member', this.get('id'));
}),

isProcessing: Ember.computed.or('createFollowup.isRunning', 'clearFollowup.isRunning', 'createContactAttempt.isRunning')
```

However, the button is still disabled before `createFollowup` and `clearFollowup` resolved, but it will be enabled once both of them finished without `createContactAttempt` being resolved.

The root cause is same as previous case,

> If the record is not yet available, the store will ask the adapter's `find` method to find the necessary data. If the record is already present in the store, it depends on the reload behavior *when* the returned promise resolves.

That said, if there is no `reload: true`, `findRecord` will resolve the old value from Ember Data first, which will lead `createContactAttempt.isRunning` to be `false` even `member` is still being fetched.

```javascript
createContactAttempt: task(function *(subject, notes, contactStatus) {
  // ...

  // it WORKS now, `createContactAttempt.isRunning` always be `true` before all `task`s resolved
  yield this.store.findRecord('member', this.get('id'), { reload: true });
})
```

## Conclusion

Like the Ember official website mentioned,

> Ember is a framework for creating **ambitious** web applications.

There are lots of advanced usage for Ember, if there is anything weird, we can always refer Ember APIs documentation first.
