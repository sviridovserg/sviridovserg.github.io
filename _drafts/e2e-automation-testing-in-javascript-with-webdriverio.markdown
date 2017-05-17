---
layout: post
title:  "End-to-end automation testing in Javascript with WebdriverIO using Mocha and Chai"
date:   2017-03-13 16:00:00

tags:
    - e2e
    - end-to-end
    - automation
    - javascript
    - webdriverio
    - mocha
    - chai
---

Building our applications we come to the point when ordinary unit tests are not enough to cover possible user scenarios and integration of different parts od the system because of complexity involved in setup of all needed dependencies. So, it means that time has come to introduce another level of tests which is based on testing application as a black box using end to end tests. Usually it happens pretty early in application development lifecycle and needed attention to guarantee high quality and stability of delivered solution.

Most of the teams have lack of dedicated automation test developers, so, software developers should also take care of automation tests. What if your engineers are experienced in fornt-end applications and has limited knowledge of back-end languages such as C# and Java? Does it mean that you cannot start test automation?

The answer is no, because you can do it using such javascript libraries as [WebdirverIO](http://webdriver.io) and [Nightwatch](http://nightwatchjs.org) which use Selenium web driver under the hood. Let's see how you can do it.

## What is WebdriverIO?

WebdriverIO provides a Selenium 2.0 binding for [NodeJS](https://nodejs.org). So, you can write end-to-end tests using Javascript and Selenium. You can write asynchronous code in a synchronous way. You can navigate easily by DOM tree using familiar queries and helper functions. It support ES6 though babel. It has a lot of different functions and features, check their web site to know more.

The WebdriverIO currently supports [Mocha](), [Jasmin]() and [Cucumber]() test frameworks

## Setup environment to develop your first tests

Starting with WebdrierIO is fairly simple because it has its own test runner, which takes care of Selenium session management and provides global proxy to Selenium functionality.

Initialize a `package.json` file in your working directory using following command

{% highlight PowerShell %}
npm init
{% endhighlight %}

This command will ask you several questions and generate file for you.

Now it is time to add WebdriverIO and Selenium standalone to your project. Run

{% highlight PowerShell %}
npm install -g selenium-standalone
npm install webdriverio
npm install wdio-selenium-standalone-service
{% endhighlight %}

Next step is to create a folder where you are going to place your end-to-end tests

{% highlight PowerShell %}
mkdir ./test/specs
{% endhighlight %}

The last step is to create configuration file for WebdriverIO. To do that use its runner. It will guide you through the process of configuration setup. Run command

{% highlight PowerShell %}
./node_modules/.bin/wdio config
{% endhighlight %}

Sweet! We are ready yo write the first test using Mocha. Create a file `test.js` in `./test/specs` folder and place the following code there

{% highlight javascript %}
var expect = require('expect');

describe('google.com', function () {
    it('should load successfully', function () {
        browser.url('http://google.com');
        expect(browser.getTitle()).to.be.equal('Google');
    });
});
{% endhighlight %}

Looks good, is not it? Lets run it and see how it goes green

{% highlight PowerShell %}
./node_modules/.bin/wdio wdio.config.json
{% endhighlight %}

Hey, do not forget to run selenium standalon too!

{% highlight PowerShell %}
selenium-standalone start
{% endhighlight %}

## First e2e tests on Mocha with Chai

These setup from previous section looks too complicated, do I need to do it for each project? And you promised me babel and ES6 support!

Of course, we always want to start doing real work as soon as possible and spend less time configuring and initializing the environment. So, good developers from WebdriverIO come up with set of boilerplates for different test and assert frameworks. Jump to the [page](http://webdriver.io/guide/getstarted/boilerplate.html) and check if the boilerplate you need is there. We will use [michaelguild13/Selenium-WebdriverIO-Mocha-Chai-Sinon-Boilerplate](https://github.com/michaelguild13/Selenium-WebdriverIO-Mocha-Chai-Sinon-Boilerplate) for this tutorial.

