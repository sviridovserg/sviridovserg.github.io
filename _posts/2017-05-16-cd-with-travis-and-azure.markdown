---
layout: post
title:  "Continues delivery pipeline using Travis and Azure on the example of React application"
date:   2017-05-16 16:00:00

desc: Continues delivery pipeline using Travis and Azure on the example of React application
keywords: CD, CI, continues delivery, travis, azure, react, build, deploy, automation, github
tags:
    - CD
    - continues delivery
    - continues integration
    - automation
    - travis
    - azure
    - react
---

There are a lot of things to consider when you start the development for new application like selecting proper technical stack, architecture, storage mechanism, etc. One of such things is configuring continues delivery pipeline. It is important for the team to introduce it on early stages to be able to release software in short cycles, improving quality and ensuring that the application is reliable.

In this blog post we will learn how to build continues delivery pipeline using [Travis][travis] and [Azure][azure] for simple [React][react] application. You will need [Node][node] and [NPM][npm] or [YARN][yarn] package manager, GitHub, Travis and Azure.

## React application

First, lets create simple React application. To do this I will use `create-react-app` utility provided by  facebook. Run your favorite console prompt and type

```
npm install -g create-react-app
```

This command will install `create-react-app` utility globally on your machine, so you will be able to run `create-react-app` command from console prompt.

To create simple React application move to the working folder and run

```
create-react-app my-app
```

Congradulations! You have just created a React app named **my-app**. Let's run it, type

```
cd my-app
npm start
```

This set of commands will move you to the `my-app` folder and run *start* npm script defined in the *scripts* section of *package.json* file.

Created application depends on `react-scripts` package which contains definition for `start`, `build`, `test`, `eject` scripts. This scripts are created using [Webpack](https://webpack.github.io). So, be aware that under the hood your application  is built with Webpack.

Now push this application to the GitHub repository.

## Travis configuration

Go to [Travis][travis] and sign in there with your GitHub account, and turn on the switch for the repository with your React application.

![Enable travis for repository](/public/images/posts/4/repo-enable-travis.PNG)

You should be able to navigate to the builds for the repository by clicking on it in the list of repositories. If you navigate there you should see the empty screen with text **"No Builds for this repository"**.

Travis is configured through a [YAML][yaml] configuration files which are stored in the same repository. It means that this file will have a change history and can be easily reverted if some breaking changes are made.

The configuration file for our React application will be

```
language: node_js
node_js: stable
sudo: required
dist: trusty
install:
  - npm cache clean
  - npm install
script:
  - npm run test
  - npm run build

```

It is a simple configuration that tells Travis
1. In that statements
    ```
    language: node_js
    node_js: stable
    sudo: required
    dist: trusty
    ```

    We will build our application using NodeJS everytime on fresh machine.
2. Next there are 2 steps:
    1. Install step that installs all the dependencies needed for our application to build
    2. Build step that is defined in `script` section. First we run `test` command to perform tests for our application, then we run `build` command to create build artefacts.

By default Travis will run build for each branch when new code is pushed.

## Azure Web Application

Now lets create Web Application in Azure where we are going to deploy our React. In [Azure Portal][azure] select  *App Services* in the sidebar

![Azure sidebar](/public/images/posts/4/azure-sidebar.PNG)

Click Add in the top bar and select *Web App* tile.

![Azure new web app](/public/images/posts/4/azure-new-web-app.PNG){:height="400px" }

Enter required data for your application like *App name*, etc. Good, now we have an web application in Azure but if you navigate to `<app-name>.azurewebsites.net` you will see 404 error. So, lets setup deployment for this application and wire it with Travis.

## Azure deployments

Select *Quickstart* under menu item in your application  sidebar menu. Then select **Node** as your development stack and select **Local Source Control** as a deployment preference

![Azure local source control](/public/images/posts/4/azure-local-source-control.PNG){:height="400px" }

Now we need to setup deployment credentials for our app to be able to give access to Travis. To do that go to your application **Settings > Deployment Credentials**

![Azure deployment credentials](/public/images/posts/4/azure-deployment-cred.PNG){:height="400px" }

Fill in the form with desired user name and password. Keep it nearby we will need it on the next step for Travis deployment.

Last thing we need to do is to add `.deployment` file to the root of our project to tell Azure which folder contains build artefact is. Add the following content to the file:

```
[config]
project = build
```

Great! Let's move to travis deployment configuration.

## Travis deployment

The last thing that should be done is connecting Travis and Azure so that when build is successful on Travis, it is automatically deployed to the Azure.

First lets add deployment credentials from Azure to the Travis as an environment variables. Go to your Travis builds for the repository and navigate to the settings from there, select **More Options > Settings**

![Travis settings](/public/images/posts/4/travis-settings.PNG)

Scroll to the **Environment Variables** section and add the following variables:

* AZURE_WA_SITE - your application name
* AZURE_WA_USERNAME - your deployment user name
* AZURE_WA_PASSWORD - your deployment password

Now we need to modify our Travis configuration to include deployment steps there. Add the following block of code to the `.travis.yml` file

```
before_deploy:
  - cd build
deploy:
  provider: azure_web_apps
  skip_cleanup: true
  verbose: true
```

First, we navigate to the `build` folder with build artefacts. Then, we setup provider for our application which is `azure_web_apps` so that Travis knows how to deploy it. Travis supports a lot of different providers, check this [page][travis_deploy] for more information. Travis deployment system will automatically leverage application name, user name and password from environment variables. `skip_cleanup` is needed so that build artifacts are not removed before deployment is finished. By default Travis will run deployment steps for any successful build made from the master branch. If you want your builds to be deployed from another branch then add the following code under `deploy` section

```
deploy:
  provider: azure_web_apps
  on: production
```

Congradulations! You've just setup simple continues delivery pipeline using Travis and Azure. You can expand it later using Azure slots to have staging environments, see [post][azure_slot] for more information.

In conclusion, in this post we've walked through the process of configuring continues delivery pipeline for React application using Travis and Azure. The pipeline is flexible and can be adapted to applications developed on Java, .Net, Python, Javascript, etc.

[travis]:https://travis-ci.org/
[azure]: https://portal.azure.com
[react]: https://facebook.github.io/react/
[node]: https://nodejs.org
[npm]: https://www.npmjs.com
[yarn]: https://yarnpkg.com
[yaml]: https://en.wikipedia.org/wiki/YAML
[azure_slot]: https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-staged-publishing
[travis_deploy]: https://docs.travis-ci.com/user/deployment/