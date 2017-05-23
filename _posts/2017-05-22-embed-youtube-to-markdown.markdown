---
layout: post
title:  "How to embed youtube video to markdown file, GitHub or GitLab comments"
abstract: "In this blog post we will learn how to embed clickable image to the youtube video using youtube url. Also, we check the online tool which will convert youtube video url automatically. "
date:   2017-05-22 16:00:00

desc: How to embed youtube video to markdown file, GitHub or GitLab comments
keywords: youtube, embed video, GitHub, GitLab, markdown, md, embed youtube to github, insert youtube to github, embed youtube to gitlab, insert youtube to gitlab
tags:
    - youtube
    - video
    - markdown
    - github
    - gitlab
---

I usually struggle to remember all the parameters for inserting links and especially images to the markdown which is the default format for GitHub, GitLab comments and documentation files and for this blog as well. Moreover, neither GitHub nor GitLab supports inserting videos.

One of the workarounds for this issue with videos is to insert an image of for the video wrapped in a link pointing to the video location. For markdown files images are inserted in the following way:

```
![alt text](http://example.com/exampl.png)
```

whereas we use the following markup to insert links

```
[link text](http://example.com/link "title")
```

So, combining these two markups allows us inserting image with link to the markdown

```
[![alt text](http://example.com/exampl.png)](http://example.com/link "title")
```

Good! Now we know how to have clickable images in our markdown which will redirect us to some link. But how can we do the same trick with youtube video?

## Inserting youtube clickable image to markdown

Let's try to do the same thing with [Audi R8](https://www.youtube.com/watch?v=KOxbO0EI4MA) youtube video.

First, we need to grab an image from the video. Hopefully there is and API to do that. You will need vide id which is alphanumeric sequence in the urls `v` parameter. In the example above it is `KOxbO0EI4MA`. The link to image will be

```
http://img.youtube.com/vi/{video-id}/0.jpg
```

For Audi R8 video the image is going to be

![Audi R8](http://img.youtube.com/vi/KOxbO0EI4MA/0.jpg)

The only one thing is left, which is combining image with url

```
[![Audi R8](http://img.youtube.com/vi/KOxbO0EI4MA/0.jpg)](https://www.youtube.com/watch?v=KOxbO0EI4MA "Audi R8")
```

Great! We have just created a clickable image that can be inserted in the markdown and used in GitHub or GitLab comments. So, now you can praise awesome commits with funny cat videos.

## Automatic Conversion

Remembering the markdown specifics to create clickable images is quite hard. So there is a web tool that's doing that automatically. Navigate to the [embedyoutube](embedyoutube.org). You will see

![embedyoutube.org blank](/assets/images/posts/5/embed-youtube-blank.PNG){:height="400px" style="max-width:100%"}

Just put there your youtube link, alt text and title for the image and you will get the markdown that is ready to be pasted to the Git comment or markdown.

![embedyoutube.org converted](/assets/images/posts/5/embed-youtube-filled.PNG){:height="400px"}

To conclude, we learned how we can embed youtube links into markdown files and GitHub and GitLab comments using image and link markup, also we learned how to do it automatically using [embedyoutube.org](embedyoutube.org)