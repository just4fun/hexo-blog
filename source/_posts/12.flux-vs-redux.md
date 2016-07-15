title: Flux vs Redux
date: 2016-07-13 15:56:34
tags:
- js
- flux
- redux

---

[Redux](https://github.com/reactjs/redux) is very popular in recent two years, it seems like it has become the standard architecture in React application.

Let's compare it with original [Flux](2015/05/12/flux-architecture/).

<!-- more -->

As Redux [official doc](http://redux.js.org/index.html) mentioned,

> Redux evolves the ideas of Flux, but avoids its complexity.

That being said, Redux is also utilizing **unidirectional** data flow.

*But what is the main difference between them?*
*And what is the complexity of Flux?*

As we know, Redux has **three principles**.

- Single source of truth
- State is read-only
- Changes are made with pure functions

IMO, the main difference between Redux and Flux is mainly focused on the first one.

## Flux Way

- **Dispatcher** distributes **Actions** to **Stores**
- **Stores** broadcast events to notify **Views** to update

We noticed that **Stores** is plural, which means we can use multiple stores in Flux architecture.

Let's go through an example from Flux source code.

> Consider there is a flight destination form, which selects a default city when a country is selected.

```javascript
let flightDispatcher = new Dispatcher();

// keeps track of which country is selected
let CountryStore = { country: null };

// keeps track of which city is selected
let CityStore = { city: null };

// keeps track of the base flight price of the selected city
let FlightPriceStore = { price: null };
```

When a user changes the selected city, we dispatch the payload.

```javascript
flightDispatcher.dispatch({
  actionType: 'city-update',
  selectedCity: 'paris'
});
```

This payload is digested by `CityStore`.

```javascript
flightDispatcher.register(function(payload) {
  if (payload.actionType === 'city-update') {
    CityStore.city = payload.selectedCity;
  }
});
```

When the user selects a country, we dispatch the payload.

```javascript
flightDispatcher.dispatch({
  actionType: 'country-update',
  selectedCountry: 'australia'
});
```

This payload is digested by **BOTH** stores.

```javascript
CountryStore.dispatchToken = flightDispatcher.register(function(payload) {
  if (payload.actionType === 'country-update') {
    CountryStore.country = payload.selectedCountry;
  }
});
```

When the callback to update `CountryStore` is registered, we save a reference to the returned token. Using this token with `waitFor()`, we can guarantee that `CountryStore` is updated before the callback that updates `CityStore` needs to query its data.

```javascript
CityStore.dispatchToken = flightDispatcher.register(function(payload) {
  if (payload.actionType === 'country-update') {
    // `CountryStore.country` may not be updated
    flightDispatcher.waitFor([CountryStore.dispatchToken]);
    // `CountryStore.country` is now guaranteed to be updated

    // select the default city for the new country
    CityStore.city = getDefaultCityForCountry(CountryStore.country);
  }
});
```

The `country-update` payload will be guaranteed to invoke the stores' registered callbacks in order: `CountryStore`, then `CityStore`.

---

You may noticed that we should do lots of stuff in Flux.

- should register callbacks both in CountryStore and CityStore to update state
- should register callbacks both in CountryView and CityView to update views (omitted in code above)
- should use `waitFor()` if we want to guarantee the update order between different stores

IMO, these are the **complexities** in Flux.

## Redux Way

In Redux, there is no Dispatcher (but there is `store.dispatch()`), moreover, there in only a **SINGLE** store, which holds the whole application state in a **SINGLE** object.

Let's update the code above in Redux approach.

First, we need to create a single store with `reducers`, then register a listener on the store. The listener will rerender whole application.

```javascript
let store = createStore(reducers);
store.subscribe(render);
```

When a user changes the selected city, we dispatch the payload, but via `store.dispatch()` instead.

```javascript
store.dispatch({
  actionType: 'city-update',
  selectedCity: 'paris'
});
```

This payload is digested by `CityReducer`.

```javascript
function city(state = {}, action) {
  switch (action.type) {
    case 'city-update':
      return object.assign({}, state, {
        city: action.selectedCity
      });
    default:
      return state;
  }
}
```

When the user selects a country, we dispatch the payload.

```javascript
store.dispatch({
  actionType: 'country-update',
  selectedCountry: 'australia'
});
```

This payload is **ONLY** digested by `CountryReducer`.

```javascript
function country(state = {}, action) {
  switch (action.type) {
    case 'country-update':
      return object.assign({}, state, {
        country: action.selectedCountry
      });
    default:
      return state;
  }
}
```

But how can we guarantee that select a country will trigger select a default city?

```javascript
componentWillReceiveProps(nextProps) {
  // check whether there is new country selected
  if (nextProps.country !== this.props.country) {
    store.dispatch({
      actionType: 'city-update',
      selectedCity: this.getDefaultCityForCountry(nextProps.country)
    });
  }
}
```

Obviously, we render our whole application two times instead of using `waitFor()` in Flux to guarantee the order.

---

Let's take a look at the steps in Redux approach.

1. should have CityReducer and CountryReducer to update state
2. only need to register **ONE** callback on the top level store to rerender
3. no need to guarantee order, just **RERENDER**

## Tips

You may noticed that we can also register the callbacks on the top level in Flux for updating Views.
In this way, we have no need to guarantee the order, just rerender like how we did in Redux.

However, there is one thing different.

```javascript
CityStore.addChangeListener(render);
CountryStore.addChangeListener(render);

// if there are other stores, we should also
// register the render callback on them.

// but in Redux, we only need to register one,
// since there is only one store holds one object.
```

Got **simplicity** of Redux now?
