title: Flux Architecture
date: 2015-05-12 23:16:05
tags:
- facebook
- flux

---

At the end of last year, there are two interesting stuff produced by Facebook came into fashion. One is [React](http://facebook.github.io/react/), another is [Flux](http://facebook.github.io/flux/).

<!-- more -->

Let's have a detailed overview about Flux.

## Overview

An application architecture that complements React’s compassable view components by utilising **unidirectional** data flow.

![unidirectional work flow](/hexo-blog/img/flux-diagram.png)

## Main Components

- Dispatcher
  - **central hub**, manages all data flow in application
  - distribute the **Actions** to the **Stores**
- Action
  - send an object (aka **payload**) contains **type** and **data** to dispatcher
  - do not contain knowledge about how to update the stores
  - invoke from event handlers of **views** or **server**
- Store
  - contain application **state** and **logic**
  - register itself with the dispatcher and provide it with a callback
  - broadcast an event to notify the views to update
- View (Controller-View)
  - get data from the stores
  - render and pass data down to its descendants

<div class="illustration">
![Flux main components](/hexo-blog/img/flux-diagram-detail.png)
</div>

## Compare with MVC

In my opinion, I'm not in favor of camparing Flux with MVC.

Flux is an architecture which help React to build client-side application. With Flux, React can be used more easily.

- store application **state** in Store, like Ember-data
- easy to **communicate** between React components
- unidirectional data flow is more **predictable**

However, if you want to use Flux **independently** without React, that maybe a little difficult. The unidirectional data flow is dependant on the core concept of React: **Virtual DOM**. Once user triggers an event from view, the flow cycle starts. When it reachs Store, it will emit an event to notify View to re-render, which is based on Virtual DOM. Therefore, you should implement a set of things which React already prepare for you.

As for MVC, it's also an architectural pattern, but it's more common. One of the disadvantages about Flux is that it's a little hard to migrate current architecture to Flux. Imagine that you have an application with one MV* framework, such as Angular or Ember. If you want to use Flux, maybe you should rebuild your whole application. It's no need to do the migration indeed.

<div class="illustration">
![MVC work flow](/hexo-blog/img/mvc-diagram.png)
</div>

What is more, I think we should compare Flux with **MVVM**, not MVC, because some frameworks like Backbone just implement MVC but has no data-binding, and the data-binding is the biggest difference from unidirectional data flow. So in my opinoin, it's boring to compare pure MVC with Flux.

<div class="illustration">
![MVVM work flow](/hexo-blog/img/mvvm-diagram.png)
</div>

## Best Practise

- ajax calls should come from Action
- distinguish between view triggered actions and server/API triggered actions
- distinguish between **stateful** Controller-Views and **stateless** Views
- **Immutable** data (See my another [post](http://just4fun.github.io/hexo-blog/2015/07/18/use-immutable-js-in-react-application/))

## Demo

I have wrote a simple demo which used React and Flux.
Just for fun.

- Repo: https://github.com/just4fun/classics/tree/master/repos/react_flux
- Live Demo: http://just4fun.github.io/classics

## References

- https://facebook.github.io/flux/docs/overview.html
- http://fluxxor.com/what-is-flux.html
- https://scotch.io/tutorials/getting-to-know-flux-the-react-js-architecture
- http://racingtadpole.com/blog/flux-react-best-practices/
