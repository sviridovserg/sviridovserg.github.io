---
layout: post
title:  "Migration from AngularJS to Angular 4"
abstract: "In this article we'll take a look on migration of SPA from AngularJS to Angular 4 and come up with draft path for it"
date:   2017-09-26 16:00:00

desc: Migration from AngularJS to Angular 4
keywords: AngularJS, Angular 4, Angular 2, Angular, Javascript, Typescript, AngularJS Migration
tags:
    - AngularJS
    - Angular 4
    - Angular 2
    - Angular
    - Javascript
    - Typescript
    - AngularJS Migration
---

Angular 2 was release about a year ago and was a complete rewrite of the previous version of the framework. Currently there are thousands lines of code written in AngularJS. Some of this applications will need to be updated to newer version of Angular. Reasons to do so can be different. For instance, application is in active development and team wants to stay up to date with technology, also it can make hiring easier, because less developers are willing to work with such and old mammoth in world of Javascript as AngularJS.

Other advantage of Angular 4 are:
* Better performance
* Better Dependency Injection mechanism
* Can use [Redux][redux] pattern to simplify data flow and state manipulation on the front-end
* Typescript, a lot of silly mistakes can be caught with proper type checks
* Component based architecture
* Using rxjs for back-end calls makes request management much easier and less error prone
* Development is easier and learning curve is more gentle
* It is better to migrate early than redevelop in future

## Migration Process

It is not easy to migrate from AngularJS to Angular 4 if your application is not following [AngualrJS Style Guide][angular-js-styleguide] and you do not follow recommendations to use [AngularJS components][angular-js-components]. The migration process consists of 3 main parts:

1. Preparation
2. Migration of the presentational components
3. Migration of the container components

See post from Dan Abramov [Presentational and Container Components][components-vs-containers] for more information about different types of components.

Let's explore each step in details.

# Preparation

First step is to introduce Typescript to your Javascript application:
* Modify build your pipeline to be able to handle js and ts files and bundle them in one file. If you use webpack for your current build you can leverage **awesome-typescript-loader** module which can work with js and process ts files with source maps.
* Add typescript types for AngularJS, Jamine and ote libraries you use
* Now you can start writing in Typescript even AngularJS components
* The next step is optional but highly recommended. Angular migration guide describes which types you can leverage for AngularJS services like $resource, $locationProvider, etc (see Switching to typescript section). Apply these types to your code

Now you are ready to gently introduce Angular 4 to your application:
* Get Angular 4 specific libraries like @angular/common, @angular/core, @angular/upgrade, rxjs, zone.js, etc
* Create simple Angular 4 module which will hold the whole application and bootstrap AngularJS app
* Modify bootstrapping process to use Angular 4 module instead of AngularJS bootstrap

{% highlight ts %}
@NgModule({
    imports: [
        BrowserModule,
        UpgradeModule
    ]
})
export class AppModule {
    constructor(private upgrade: UpgradeModule) { }
    ngDoBootstrap() {
        this.upgrade.bootstrap(document.body, ['myapp']);
    }
}
{% endhighlight %}

* Add new bundle to the build and index files
* Check that everything works as expected and application starts

# Migration of the presentational components

Your presentation components should not depend on the services, which usually used to share data access behaviour and cross component communications. So, steps to migrate them are straightforward:

* Migrate js file to typescript and fix type errors
* Change directive to Angular Component and use appropriate life-cycle events to have the same behaviour
* Downgraded new Component and registered it also in AngularJS application so that it can be used in AngularJS components
{% highlight ts %}
const myApp = angular.module('myApp.module');

myApp.directive(
    'myDowngradedDirective',
    downgradeComponent({component: MyAngular4Component}) as angular.IDirectiveFactory
);
{% endhighlight %}
* Run application and check that everything works as expected

# Migration of the container components

Container components usually orchestrate some part of the state of the application, therefore it can use several application services to get or update data or perform other actions. Now you have 2 choices. First, you can migrate your container component to Angular 4 and implement providers for your AngularJS services to be able to use them (see [Making AngularJS Dependencies Injectable to Angular][angularjs-dependencies]). Then migrate your services and remove providers. Another choice is to migrate services first so that you are able to use them in Angular 4 components and downgrade them if there is a need to use them in AngularJS code (see [Making Angular Dependencies Injectable to AngularJS][angular-dependencies]). Afterwards migrate the component to Angular 4.

# Conclusion

We have explored the path to migrate AngularJS application to Angular 4. Depending on application this can take a lot of time. The main disadvantage of the upgrade process is that there will be 2 versions of the framework (AngularJS and  Angular) which coexist together. This means we need to load them both to the page and do extra coding to let components work together.

The resources I found useful to understand the migration path and what it takes to move to the next framework:
- [Javascript to Typescript migration guide][javascript-to-typescript]
- [AngularJS to Angular migration guide][angularjs-angular-migration]
- [Angular tutorials][angular], go there to get more familiar with Angular 4, syntax and features

[redux]:http://redux.js.org/
[angular-js-styleguide]:https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md
[angular-js-components]:https://toddmotto.com/exploring-the-angular-1-5-component-method/
[components-vs-containers]:https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0
[javascript-to-typescript]:https://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html
[angularjs-angular-migration]:https://angular.io/guide/upgrade
[angular]:https://angular.io/
[angularjs-dependencies]:https://angular.io/guide/upgrade#making-angularjs-dependencies-injectable-to-angular
[angular-dependencies]: https://angular.io/guide/upgrade#making-angular-dependencies-injectable-to-angularjs