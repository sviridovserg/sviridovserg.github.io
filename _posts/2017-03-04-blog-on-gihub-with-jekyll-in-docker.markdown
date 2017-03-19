---
layout: post
title:  "Blog on GitHub Pages with Jekyll in Docker for Windows users"
date:   2017-03-04 16:00:00

tags:
    - jekyll
    - GitHub pages
    - Docker
    - Windows
---


So you want to start blogging but do not know how. Let's discover one of the ways to do it using Jekyll and GitHub. Why GitHub? Because we're developers and it is the tool that we use everyday, so why not use it for blogging as well?


## How to create a blog on GitHub Pages?

Obviously you need to have a [GitHub](https://github.com) account, if you still do not have one go get it.

Once you have your account, go to your profile and create new repository named <b>username.gihub.io</b>, where <b>username</b> is your GitHub username.

Now, clone this repository to your machine and you're ready to create your site files and push them to GitHub. Once they are pushed, your site is available using http://username.gihub.io link.


## What is Jekyll?

[Jekyll](http://jekyllrb.com) allows you to generate static website from your text files, usually markdown files. It is easy to use for blogging, it supports categories, posts, permalinks and has a lot of themes to apply for your blog. Moreover, GitHub Pages are powered by Jekyll.


## Sounds good, but why only Windows users?

The main reason is that the official installation for Jekyll is only available for GNU/Linux, Unix, or macOS operating systems.

Another reason is to keep your OS clean if you're as reluctant to install extra software as I am, because to run Jekyll on Windows you'll need to install Ruby, gems, etc.

So, we can use [Docker](https://Docker.com) to run Jekyll inside it to avoid installing extra software on your machine. It also allows you to use it in the OS it is meant to be used, because there are plenty of containers and most are based on Unix-like systems. Of course, there is one that has everything needed to run Jekyll.


## Ok, I am a Windows user and want to write a blog using Jekyll

Using Docker is fairly simple. Create a Docker file named <i>Dockerfile</i> and place the following content there:

{% highlight PowerShell %}

FROM jekyll/jekyll:pages
VOLUME /srv/jekyll
EXPOSE 4000
CMD jekyll serve --force_polling

{% endhighlight %}

First, you specify here which image to use. Mount a volume in your container for your blog sources at `/srv/jekyll`. Next, you expose the 4000 port of the container, which is usually used by Jekyll server. Finally, you put the command which is going to be run by default when the container starts

Now we need to build our Docker image. To do that just run:

{% highlight PowerShell %}

Docker build -t <container-name> .

{% endhighlight %}

Where `<container-name>` is the tag for newly created container.

Well done! Only a few steps left. Now, let's run our container and see how our blog looks like. Run the following command:

{% highlight PowerShell %}

 Docker run -w /srv/jekyll -v <absolute-path-to-folder>:/srv/jekyll -p=4000:4000 <container-name>

{% endhighlight %}

Here we are instructing Docker to run our container with working folder `/srv/jekyll`, mapping our folder with blog sources to the working folder in the container and mapping the 4000 port on localhost to the 4000 port on the container.

Now, just open your favorite browser and navigate to [http://localhost:4000](http://localhost:4000) to see your blog.