---
layout: post
title:  "Access elements in automation tests  for web applications"
abstract: "In this article we will learn how to organize access to the elements in end-to-end automation tests for web applications"
date:   2017-08-01 16:00:00

desc: Access elements in automation tests for web applications
keywords: automation tests, tests, Selenium, data-automation-id, PageObject, end-to-end tests, automation, access elements, Java
tags:
    - automation tests
    - tests
    - Selenium
    - data-automation-id
    - PageObject
    - end-to-end tests
    - automation
    - access elements
    - Java
---

Usually the last thing developers think of in the beginning of the new project or feature is an automation testing for the software. They spend hours to analyze requirements, design architecture, create tests suites for unit tests, whereas automation tests are implemented in the later phase by testers or dedicated automation engineer. This practice leads to very inconvenient ways to select elements from automation test suites.

## How you can find element on the page?

There are mainly two approaches to find element on the page from automation test suite. No matter whether you use Selenium WebDriver directly from Java, C#, etc. or you use bindings to Selenium like WebdriverIO, Nightwatch in javascript, you can use the first approach which is **xPath** selectors or the second on which is **CSS** selectors. For web applications I prefer to use CSS selectors because they are easier to understand for developers and all modern browser Developer Tools show element's CSS classes. Another advantage of the CSS selectors is that they are less dependent on document structure than xPath selectors.

Nevertheless automation testers need to leverage whatever CSS classes, ids or other attributes are there to find element and it is common to see selectors like:

```
table.orders .row .td:nth-child(3) input
```

or even worse. Development, support and modification of tests based on such selectors is very hard, error prone and time consuming.

Even if developers care about automation tests and add special classes or elements' ids for them still it introduces additional issues. For instance, someone can refactor HTML and remove or rename unused classes or element identificators which will lead to test failures and long debugging sessions to find the root causes of the issue. Also, it is quite hard to strip code from this additional classes for production builds.

## Simplifying access to the elements

There is one approach that can dramatically reduce complexity of the CSS selectors in automation tests. You need to come up with the attribute dedicated purely for automation tests, for instance `data-automation-id`. Work closely with developers to add this automation identificators to any element that can be accessed from automation tests.

There are couple of rules to follow when you are using automation ids:

* Use unique idnetificators for different elements or set of elements
* Use same identificator for elements in collections (e.g. items in lists can have `data-automation-id="user-list-item"`)
* Name your identificators to reflect element's business value

Using this rules you will be able to access elements on the page pretty easily. For instance, to get user login or password you can use the following selector

```
[data-automation-id="app-login-form"] [data-automation-id="login-input"]
```

The downside of this approach is that automation engineer should work closely with developers and special attributes should be included into the code from the very beginning. This can be achieved by a code review practice and custom linting rules as well.

In conclusion, in this blog post we've learnt how to simplify access to the elements from automation tests using special attribute dedicated for precisely identifying element on the page.