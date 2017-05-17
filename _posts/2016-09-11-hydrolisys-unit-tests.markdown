---
layout: post
title:  "Using analyzer to filter unit tests in polymer project"
date:   2016-09-01 10:00:00
categories: polymer
tags: 
    - polymer
    - polymer analyzer
    - polymer hydrolisys
---

So you've started your first polymer project and it's going well. After a while you noticed that you have over a hundred of components. Because you're a good developer you write unit tests for your components and run them as part of your CI/CD pipeline. It means that each small change in one component leads to execution of thousand tests and increase in build time. This blog will provide one of the solutions to this issue.

## What is polymer analyzer?

[Polymer analyzer](https://github.com/Polymer/polymer-analyzer "Polymer analyzer") is a javascript library developed by Google to perform static analysis for web components. Until version 2 it was called Hydrolysis. In this article 1st version is used.

## So, how can we use it?

Analyzer accepts path to html document with Polymer component and provides information about the component and its dependencies. If dependencies are included in the file as links to other html files then analyzer will provide paths to these files. It means that we can build dependency tree for any polymer component.

There are several assumptions made in this blog post for implementation:

<ol>
    <li>GIT is used as VCS</li>
    <li>Each component is placed in folder</li>
    <li>Tests for each component are placed in "component_folder\tests" folder</li>
</ol>

Great! Let's start coding!

First of all we need to retrieve a changeset for the repository. Let's use as an input for changeset function we'll use a commit hash and path to folder. We need to include promise module to be able to use promises in our module and exec function from child_process module.

{% highlight javascript %}

function getChangeset(folderPath, commitHash) {
    return new Promise(function(resolve, reject) {
        execInFolder(folderPath, function() {
            var changedFilesCmd = util.format('git diff --name-only %s HEAD', commitHash);
            exec(changedFilesCmd, function(err, stdout, stderr) {
                if (stdout === '') {
                    resolve([]);
                    return;
                }
                resolve(_.filter(stdout.split('\n'), function(fn) { return fn !== ''; }));
            });
        });
        
    });
}

function execInFolder(folderPath, cmd) {
    var wd = process.cwd();
    try {
        process.chdir(folderPath);
    } catch {
        throw new Error('No such folder');
    }
    cmd();
    process.chdir(wd);
}

{% endhighlight %}

Now as we have a list of changed files we can finally implement our Polymer components traverse function to collect unit tests that should be executed during our build process

{% highlight javascript %}

function aggregateUnitTests(changeset, unittests, pathToComponent) {
    var name = path.basename(pathToComponent);
    if (name.indexOf('myprefix-') !== 0) {
        return new Promise(function(resolve) { resolve(false); });
    }
    return new Promise(function(resolve) {

        hydrolysis.Analyzer.analyze(pathToComponent).then(function(analyzer) {
            var dependencies = _.chain(analyzer.html)
                .filter(function(d) { return d.href !== pathToComponent })
                .map(function(d) { return d.href })
                .value();
            var shouldAdd = false;
            if (_.chain(changeset).filter(function(c) { return c.indexOf(name) !== -1}).some().value()
                || areDependenciesInChangeset(dependencies, changeset)) {
                shouldAdd = true
            }
            if (dependencies.length > 0) {
                Promise.all(_.map(dependencies, function(d) { return getUnitTests(chageset, unittests, d); })).then(function(res) {
                    var addUnitTests = shouldAdd || _.some(res, function(r) { return r; }); 
                    if (addUnitTests) {
                        unittests.push(getTestPath(pathToComponent))
                    }
                    resolve(addUnitTests);
                });
            } else {
                if (addUnitTests) {
                    unittests.push(getTestPath(pathToComponent))
                }
                resolve(addUnitTests);
            }
        });
    }); 
}

{% endhighlight %}

This function parse component to get its dependencies, if component is in changeset or if one of dependencies in a changeset then components' unit tests are added to the list, otherwise it should not be added to the list of unit tests.
Now we can compose these functions into working solutions

{% highlight javascript %}

function getUnitTests(repoPath, commitHash) {
    var appComponentPath = getAppComponent(repoPath);
    return new Promise(function(resolve) {
        getChangeset(repoPath, commitHash).then(function(chageset) {
            var unittests = [];
            aggregateUnitTests(chageset, unittests, appComponentPath).then(function() {
                resolve(_.unique(unittests));
            });
        });
    });
}

{% endhighlight %}

Finally, we can use getUnitTests function in out build process. For instance, it can be a step in our gulp build that executes javascript unit tests to verify build.