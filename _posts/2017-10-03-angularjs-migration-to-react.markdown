---
layout: post
title:  "Migration from AngularJS to React"
abstract: "Let's take a look on rough plan for migration AngularJS application to the React"
date:   2017-10-02 16:00:00

desc: Migration from AngularJS to React
keywords: AngularJS, React, Redux, Javascript, AngularJS Migration
tags:
    - AngularJS
    - React
    - Redux
    - Javascript
    - AngularJS Migration
---

Probably you started exploring different way to migrate your old AngularJS application to newer framework. One of the available approaches is to use [React][react]. React was released in 2013 and became very popular since then. It is a stable library that has been adopted by a lot of companies. If you still concerned about React here are some advantages and disadvantage of using it.

So, why React when you have Angular project. Here are advantages:
* It is more stable, was released long before Angular 2/4
* Virtual DOM makes updates faster
* One-way data flow only, can use [Redux][redux] to manage state
* Can use typescript as well
* Component based development
* Easier to find help in the internet (AngularJS vs Angular)

Also, you should keep in mind the following disadvantages as well:
* Comparing to Angular, React is not a framework, it is just view library, so you need to use additional libs to manage state like * [Redux][redux], [Flux][flux], [MobX][mobx], etc, or to access back-end like [Fetch][fetch]
* JSX is not a real html and you need to get used to it
* More boilerplate code to make it work

## Preparation

First, I need to mention that it is much easier to migrate application that is following [AngualrJS Style Guide][angular-js-styleguide]. So, if you are not doing it right now I highly recommend to start doing that. Before migration you will need to add `react` and `react-dom` packages to your application. Modify your build process, so that it can process React components. If you use webpack you can use [babel-preset-react][babel-preset-react] for it. Add [react-app][react-app] plugin for eslint to be able to lint your newly created React components.

## Process

Generally, the upgrade process from AngularJS to React consists of 2 phases:

1. Migration of the presentational components
2. Migration of the container components

If you want to know the diference between these two types of components see post from Dan Abramov [Presentational and Container Components][components-vs-containers]. React nothing more than just a view library, so, you can use it with AngularJS as a rendering mechanism. It is easy to start transformation of simple components to React pretty fast and you do not need to write extra code for bootstrapping application or reusing services from one framework in another like you should if you migrate to Angular 2/4. So, there are 2 approaches to make React work with AngularJS:
* Use custom directives to wrap your React components and pass bindings to `props` (see [blog post from Small Improvements][react-angularjs-1])
* Use wrapper library like [ngReact][ngreact] (see [blog post from James Ravenscroft][react-angularjs-2])

In this post we will discover the second approach, because it requires less extra code and does not require manual render of the React component. So, start conversion from presentational components using correct life-cycle events to manage your component.

{% highlight js %}
class MyTitleComponent extends Component {
    redner() {
        return (
            <label className="title">{this.props.title}</label>
        )
    }
}
{% endhighlight %}

Once you convert your component you can use it in AngulatJS directives using `reactDirective` provided by ngReact. To wrap your simple component use the following code:

{% highlight js %}
const app = angular.module('myapp');

app.directive('myTitle', ['reactDirective', function(reactDirective) {
    return reactDirective(MyTitleComponent);
}]);
{% endhighlight %}

Doing that you will create simple wrapper directive that now can be used from AngularJS directives that are not converted yet. When all presentational components are converted you can start conversion of container components and introduce state management library. You can remove wrappers when all directives converted. The final step is moving to React router and removing AngularJS dependencies.

## Conclusion

In this post we observed possible ways to migrate AngularJS application to React with the help of ngReact. Initial setup is smaller then Angular 2/4, but it does not use any state management library, back-end access library, etc, which are included in Angular out of the box. Also, all AngularJS directives that wrap React will have only one way data-binding so to modify anything you need to move all state management to parent component and do necessary changes to the data yourself. Another limitation is that you cannot use AngularJS directives in React components, there is no transclude in ngReact.

[react]:https://reactjs.org/
[redux]:http://redux.js.org/
[flux]:https://facebook.github.io/flux/
[mobx]:https://facebook.github.io/flux/
[fetch]:https://github.com/github/fetch
[react-angularjs-1]:https://tech.small-improvements.com/2017/01/25/how-to-migrate-an-angularjs-1-app-to-react/
[react-angularjs-2]:https://medium.com/@jrwebdev/migrating-an-angular-1-application-to-react-8891ec73d462
[ngreact]:https://github.com/ngReact/ngReact
[components-vs-containers]:https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
[angular-js-styleguide]:https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md
[babel-preset-react]:https://www.npmjs.com/package/babel-preset-react
[react-app]:https://www.npmjs.com/package/eslint-config-react-app