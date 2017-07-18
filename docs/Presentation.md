---
title: Redux Introduction
revealOptions:
    transition: 'none'
---

# Redux

---

Histoire : Redux : Reduce + Flux
Without redux: hoist

But : Front-end app scale
Gérer simplement des app complexes
poser des contraintes pour avoir des garanties
  => tradeoffs
    - Describe app state as array or object
    - Describe changes as plain objects
    - Describe logic to handle changes as pure fonctions

  What we get
    - Debug with UI state
    - Decouple what happened from how thing changes

Immutable store ? Mutation tracking

Developer friendly : tool demo

----
----
----
----

---

Casting

---

## DEV : Action creator

![](imgs/action_creator.png)

A function returning an action. <!-- .element: class="fragment" data-fragment-index="1" -->

An action is an object having at least a type key which has a string value <!-- .element: class="fragment" data-fragment-index="2" -->

----

```
const editPDS = description => ({
  type: 'EDIT_PDS',
  description,
});
```

---

## Store

Redux : Holds state of the app as the single source of truth.

![](imgs/store.png)

----

Redux store shape

![](imgs/redux_store.png)

---

## Reducers

![](imgs/reducer.png)

Creates new versions of the state using action and current state. <!-- .element: class="fragment" data-fragment-index="1" -->

----

Basic reducer

```
const orders = (state = false, action) => {
  if (action.type === 'ACCEPT_ORDER') {
      return true;
  }

  return state;
};
```

Always return state ! <!-- .element: class="fragment" data-fragment-index="1" -->

----

Classic pattern : reducer API

```
const orders = (state, action) => {















};
```

----

Switch on action type

```
const orders = (state, action) => {
  switch (action.type) {













  }
};
```

----

Return state

```
const orders = (state, action) => {
  switch (action.type) {











    default:
      return state;
  }
};
```

----

Add some logic

```
const orders = (state, action) => {
  switch (action.type) {
    case 'ACCEPT_ORDER':
      return state.map(order => {
        if(order.id === action.id) {
            return {...order, accepted: true};
        }
      });





    default:
      return state;
  }
};
```

----

Add some logic (again)

```
const orders = (state, action) => {
  switch (action.type) {
    case 'ACCEPT_ORDER':
      return state.map(order => {
        if(order.id === action.id) {
            return {...order, accepted: true};
        }
      });
    case 'REJECT_ORDER':
      return state.map(order => {
        if(order.id === action.id) {
            return {...order, accepted: false};
        }
      });
    default:
      return state;
  }
};
```

----

Combine reducers

```
import { combineReducers } from 'redux';
import orders from './ordersDuck';
import offers from './offersDuck';

const reducers = combineReducers({
  orders,
  offers
});

export default reducers;
```

---

## Views

![](imgs/views.png)

----

React stuff:

* A container is bound to business terms
* A component is not business aware
* Internal state should not be used

---

## View layer binding

![](imgs/binder.png)

----

Map Redux state to container props

```
const mapStateToProps = state => {
  return {
    id: state.currentPDS.id,
    description: state.currentPDS.description,
  };
};
```

Map store dispatch to container props <!-- .element: class="fragment" data-fragment-index="1" -->

```
const mapDispatchToProps = dispatch => {
  return {
    editPDS: description => dispatch(editPDS(description)),
  };
};
```
<!-- .element: class="fragment" data-fragment-index="1" -->

Connect store and view <!-- .element: class="fragment" data-fragment-index="2" -->

```
connect(mapStateToProps, mapDispatchToProps)(PDSEditForm);
```
<!-- .element: class="fragment" data-fragment-index="2" -->


---

## Root component

![](imgs/root.png)

----

```
import { createStore } from 'redux';
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```
// not redux ! It is a sugarcoat
import { Provider } from 'react-redux';
```
<!-- .element: class="fragment" data-fragment-index="2" -->
```
import reducers  from './redux';
// Init store
const store = createStore(reducers);
```
<!-- .element: class="fragment" data-fragment-index="1" -->
```
const Root = () => (
```
<!-- .element: class="fragment" data-fragment-index="0" -->
```
  <Provider store={store}>
```
<!-- .element: class="fragment" data-fragment-index="2" -->
```
    <div>
      <ListMessage/>
      <AddMessage/>
    </div>
```
<!-- .element: class="fragment" data-fragment-index="0" -->
```
  </Provider>
```
<!-- .element: class="fragment" data-fragment-index="2" -->
```
);
```
<!-- .element: class="fragment" data-fragment-index="0" -->

----

```
import { createStore } from 'redux';

// not redux ! It is a sugarcoat
import { Provider } from 'react-redux';

import reducers  from './redux';
// Init store
const store = createStore(reducers);

const Root = () => (
  <Provider store={store}>
    <div>
      <ListMessage/>
      <AddMessage/>
    </div>
  </Provider>
);
```

---

## ⏪ Recap

---

## Init store

![](imgs/start_1.png)

----

Reducers have the same shape as the store.

```
import {createStore, applyMiddleware} from 'redux';
import todoApp from './reducers';
import thunk from 'redux-thunk';
import createLogger from 'redux-logger';

const configureStore = () => {
  //only plain object reach createLogger middleware and then reducers
  const middlewares = [thunk];
  if (process.env.NODE_ENV !== 'production') {
    middlewares.push(createLogger());
  }

  return createStore(todoApp, applyMiddleware(...middlewares));
};

export default configureStore;
```

---

## Connect store and components

![](imgs/start_2.png)

----

Inject into root component

```
const store = configureStore();

ReactDOM.render(
  <Root store={store}/>, document.getElementById('mount-point'));
```

---

## Prepare action Callbacks

![](imgs/start_3.png)

----

Map dispatch to props

```
const mapDispatchToProps = dispatch => {
  return bindActionCreators({
    sendMessage,
    changeNewMessage,
  }, dispatch);
};

export default connect(mapStateToProps, mapDispatchToProps)(AddMessage);`
```

---

## Workflow

![](imgs/redux.gif)


---


### Credits

> Lin Clark : code-cartoons.com
