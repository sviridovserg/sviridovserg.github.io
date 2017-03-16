---
layout: post
title:  "Blog on GitHub pages with jekyll in docker for windows users"
date:   2017-03-04 16:00:00

tags: 
    - jekyll
    - GitHub pages
    - Docker
    - Windows
---

So, you want to start blogging but do not know how. Let's discover one of the ways to do it using jekyll and GitHub. Why GitHub? Cause we're developers and it is the tool that we use everyday, so, why not to use it for blogging as well?


## How to create a blog on GitHub pages?

Obviously, you need to have a [GitHub](https://github.com) account, if you still do not have one go get it.

Once you have your account, go to your profile and create new repository named <b>username.github.io</b>, where <b>username</b> is your GitHub username.

Now, clone this repository to your machine, and you're ready to create your site files and push it to github. Once it pushed, it is available using http://username.github.io link.


## What is Jekyll?

[Jekyll](http://jekyllrb.com) allows you to generate static website from your text files, usually markdown files. It is easy to use for blogging, it supports categories, posts, permalinks and has a lot of themes to apply for your blog. Moreover, GitHub pages are empowered by jekyll.

## Sounds good, but why only windows users?

The main reason is that official installation for jekyll is only available for GNU/Linux, Unix, or MacOS operating systems. 

Another reason is to keep your OS clean if you're same reluctant to install extra software person as I am, because to run jekyll on windows you'll need to install Ruby, gems, etc.

So, we can use [Docker](https://docker.com) to run jekyll inside it to avoid installing extra software on your machine and to use it in the OS it is meant to be used, because there are plenty of containers and most of them are based on Unix-like systems. Of course, there is one that has everything needed to run jekyll.

## Ok, I am windows user and want to write a blog using Jekyll

Using docker is fairly simple. Create a docker file named <i>Dockerfile</i> and place the following content there

{% highlight PowerShell %}

FROM jekyll/jekyll:pages
VOLUME /srv/jekyll
EXPOSE 4000
CMD jekyll serve --force_polling

{% endhighlight %}

First, you specify here which image to use. Mount a volume in your container for your blog sources `/srv/jekyll`. Next, you expose 4000 port on the container, which is usually used by jekyll server, and, finally, you put the command which is going to be run by default when container starts.

Now we need to build our docker image. To do that just run

{% highlight PowerShell %}

docker build -t <container-name> .

{% endhighlight %}

Where `<container-name>` is the tag for newly created container.

Well done! Only few steps left. Now let's run our container and see how our blog looks like. Run the following command

{% highlight PowerShell %}

 docker run -w /srv/jekyll -v <absolute-path-to-folder>:/srv/jekyll -p=4000:4000 <container-name>

{% endhighlight %}

Here we are telling docker to run our container with working folder "/srv/jekyll", mapping our folder with blog sources to working folder in the container and mapping 4000 port on localhost to 4000 port on the container.

Now just open your favorite browser and navigate to [http://localhost:4000](http://localhost:4000) to see your blog.