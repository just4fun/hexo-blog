title: My first iOS app built with React Native
date: 2016-03-30 11:10:56
tags:
- js
- app
- react native
- facebook

---

In the latter half of last year, after I created [a simple books searching demo](http://just4fun.github.io/classics) via React + Flux + Immutable.js (I also mentioned it in last two articles), I have really fallen in love with unidirectional data flow as also the amazing technology stack.

<!-- more -->

## Motivation

At that time, there is a Flux-liked architecture called `Redux` appeared in my eyes, even more surprising was that it gained lots of fans and stars in a short time (there will be another separate article for Redux). I really wanna try it to build a cool stuff with any other framework.

I found there is no official app for [the BBS of my university](http://bbs.uestc.edu.cn/) (the BBS site is powerdy by Discuz!, and there is only app which converted from Discuz! site by [appbyme](http://www.appbyme.com/)), so why not build a customized app with React Native? There is almost no learning curve if you are familiar with React.

## Infrastructure

When I started work on the basic infrastructure of the app, I had some troubles.

- Some ES6/ES7 features can not be used (I stared with React Native 0.12.0)
- [Official documents](http://facebook.github.io/react-native/docs/getting-started.html) have no UI demostration for each component
- There is almost no React Native app on Github for reference in addition to some small demos

Anyway, the hardest part is actually in the beginning.

- Bring Webpack and Babel in (*Unnecessary*)
- Try each component in project to see the UI and behaviour (*Unnecessary*)
- Why not become the reference on Github for other React Native developers? (*Cool*)

With the basic development environment, I started work on BBS features after I read some [examples](https://github.com/reactjs/redux/tree/master/examples) of Redux.

## Development

Facebook just provided basic components to developers, if you need some customized features or components, you must wrote your own by creating bridge between JavaScript and Objective C. Unfortunately, I'm not familiar with it.

Then I found there are already [cool components](http://www.gajotres.net/must-have-plugins-for-react-native/) in the community, most of them are still being built, so I chose some of them.

- [react-native-side-menu](https://github.com/react-native-fellowship/react-native-side-menu): for basic layout
- [react-native-refreshable-listview](https://github.com/jsdf/react-native-refreshable-listview): for refershable listview
- [react-native-vector-icons](https://github.com/oblador/react-native-vector-icons): for icons in my project

There is really no learing curve for me, just need minimal changes.

- Use tag `<View>` instead of `<div>`
- Use Flexbox-liked layout with JS instead of CSS

## Best practise

- If you use React Native < 0.16.0, add `.babelrc` instead of Babel plugin in your root if you want to use more ES6/ES7 features
  - If you use React Native >= 0.16.0, you can not use `decorator` of ES7 because of Babel 6 (refer [here](https://phabricator.babeljs.io/T2645))
- Refer [UIExplorer](https://github.com/facebook/react-native/tree/master/Examples/UIExplorer) example provided by React Native repo for component UI and funcationlities
  - It's better to build this example on your device for consulting components usage everywhere
- Change `Build Configuration` from `Debug` to `Release` when using offline bundle, or the performance on your device will suffer greatly

## What I gain

- 67 stars & 20 forks on Github (as of the article publish date)
  - This is my first Github repo which gained attention from other developers, they treated this project as tutorials for React Native or Redux, and even ask me for the BBS account. It encouraged me to add new features or refactor some existed code 
- Get more understanding in React
  - From React 0.14, it has been split into `react` and `react-dom` (refer [here](https://facebook.github.io/react/blog/2015/10/07/react-v0.14.html#two-packages-react-and-react-dom)), that said the essence of React Native is same with React, so that's why I said "there is almost no learning curve if you are familiar with React"
- Want to learn new skill
  - There is an [issue](https://github.com/facebook/react-native/issues/5616) in my project, it seems like an unsupported feature in React Native. If I get familiar with Objective C, maybe I could create a PR for React Native

## Repository

https://github.com/just4fun/uestc-bbs-react-native

## Screenshot

![](https://cloud.githubusercontent.com/assets/7512625/13497473/54ac771a-e190-11e5-9a63-944ed8f836a1.gif)
