---
layout: post
title:  "Navigation in automation tests scenarios implemented in Gherkin language with PageObject pattern"
abstract: "In this article we will learn how to organize navigation between pages in automation tests scenarios that are implemented with Gherkin language (e.g. Cucumber, SpecFlow) and PageObject pattern."
date:   2017-05-30 16:00:00

desc: Navigation in automation tests scenarios written in Gherkin language with PageObject pattern
keywords: automation tests, tests, Selenium, SpecFlow, Cucumber, PageObject, end-to-end tests, automation, navigation, Java
tags:
    - automation tests
    - Selenium
    - SpecFlow
    - Cucumber
    - PageObject
    - end-to-end tests
    - navigation in tests
    - navigation
    - Java
---

There are two approaches for implementation automated test scenarios: implement steps in code or using [BDD][bdd]. First leverages test frameworks such as [JUnit][junit], [xUnit][xUnit], [Mocha][mocha], etc., where tests are implemented in one function. This function will consist of test initialization, action and assertion. Usually you use [PageObject][page_object] pattern to abstract your page specifics from test implementation details. You create, modify and destroy PageObjects in the same function:

{% highlight java %}
@Test
public void testIsActionDone() {
    MainPage page = new MainPage(driver);
    page.action();
    assertTrue(page.isActionDone);
}
{% endhighlight %}

So, you can use PageObjects to navigate between pages as well, the way it is described in Martin Fowler's article. Briefly, each page will return next page if navigation performed

{% highlight java %}
@Test
public void testIsSubActionDone() {
    SubPage page = (new MainPage(driver)).navigateToSubPage();
    page.action();
    assertTrue(page.isActionDone);
}
{% endhighlight %}

Another approach for implementation automated test scenarios is to use one of the frameworks based on Gherkin language such as [Cucumber][cucumber], [SpecFlow][spec_flow] and implement tests using BDD. It means, that test scenario is described as a set of steps, each step is implemented as a separate function in the code

{% highlight Gherkin  %}
Scenario: Check that action is done
    When I am on the main page
    And I perform action
    Then Action is performed
{% endhighlight %}

Where the steps are

{% highlight java %}
public class Steps {
    MainPage page;

    @When("^I am on the main page")
    public void whenOnTheMainPage() {
        page = new MainPage(driver);
        page.openPage();
    }

    @And("^I perform action")
    public void whenPerformAction() {
        page.action();
    }

    @Then("^Action is performed")
    public void verifyActionDone() {
        assertTrue(page.isActionDone);
    }
}

{% endhighlight %}

Usually steps are defined in different files, which makes it hard using navigation approach described above. Each step definition in the scenario should be self contained and do not share state between each other. So, to implement navigation and be able to use different PageObjects in steps we will implement helper function which is going to check if given PageObject represents current page and create it automatically.

{% highlight java %}
public class BasePage {
    public static <T extends AbstractPage> T onPage(Class<T> cl) {
        T page = PageFactory.initElements(DriverManager.getDriver(), cl);
        if (page.isOnPage()) {
            return page;
        }
        return null;
    }
}
{% endhighlight %}

The only requirement for this function to work is: each PageObject should implement method `isOnPage`, which returns `true` if PageObject represents current page, otherwise `false`. Now PageObject organizes navigation to the next page if needed and waits till the page is loaded? but instead of returning new PageObject we use `onPage` function. Thus, implementation for `testIsSubActionDone` scenario using BDD will be

{% highlight Gherkin  %}
Scenario: Check that action is done
    When I am on the main page
    And I go to the sub page
    And I perform action
    Then Action is performed
{% endhighlight %}

{% highlight java %}
public class MainSteps {
    @When("^I am on the main page")
    public void whenOnTheMainPage() {
        page = new MainPage(driver);
        page.openPage();
    }

    @And("^I go to the sub page")
    public void whenGoToTheSubPage() {
        onPage(MainPage.class).action();
    }
}

public class SubSteps {
    @And("^I perform action")
    public void whenPerformAction() {
        onPage(SubPage.class).action();
    }

    @Then("^Action is performed")
    public void verifyActionDone() {
        assertTrue(onPage(SubPage.class).isActionDone);
    }
}
{% endhighlight %}

As a result, we learnt how to leverage `onPage` method to organize navigation between pages in automated test scenarios, which are implemented using Cucumber, SpecFlow or another framework based on Gherkin language and use PageObjects to abstract from page implementation details.

[page_object]:https://martinfowler.com/bliki/PageObject.html
[junit]: http://junit.org
[xUnit]: https://xunit.github.io/
[mocha]: https://mochajs.org/
[cucumber]: https://cucumber.io
[spec_flow]: https://specflow.org/
[tdd]: https://en.wikipedia.org/wiki/Test-driven_development
[bdd]: https://en.wikipedia.org/wiki/Behavior-driven_development