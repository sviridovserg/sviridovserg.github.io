---
layout: post
title:  "Capturing photo from web application with examples on React"
abstract: "This article teaches how to implement simple user photo capturing using HTML 5 a bit of Javascript with examples on React"
date:   2017-09-18 16:00:00

desc: Capturing photo from web application with examples on React
keywords: Capture photo, Make photo, HTML 5, React, Javascript
tags:
    - capture photo
    - Make photo
    - HTML 5
    - React
    - Javascript
---

Desktop and especially mobile devices are very powerful nowadays and the web tools for them has heavily matured recent years. With release of HTML 5 it became much easier to implement photo capturing feature on the web page than it used to be. To make a photo and then draw it using HTML 5 we will use `video` and `canvas` elements along with React to glue everything together. You can check [David Walsh blog post][DavidWalsh_Blog] for the pure Javascript example.

## Building React Photo Shooter

First lets create a component which will render a an element displaying real-time camera captured stream. We will use `video` html tag and `navigator` to start capturing camera input. We need to call `getUserMedia` function from navigator once React element is mounted, so we use `componentDidMount` life cycle event handler to perform this action. Also we need to interact with the video element, we will access it using [refs][refs]. See the code below

{% highlight javascript  %}
class PhotoCapture extends Component {
  constructor(props) {
    super(props);
  }
  componentDidMount() {
    navigator.getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia || navigator.msGetUserMedia || navigator.oGetUserMedia;
    if (navigator.getUserMedia) {
        navigator.getUserMedia({video: true}, this.handleVideo, this.videoError);
    }
  }
  handleVideo (stream) {
    // Update the state, triggering the component to re-render with the correct stream
    this.setState({ videoSrc: window.URL.createObjectURL(stream) });
    this.videoElement.play();
  }
  videoError() {
  }
  render() {
    const video = (<video id="video" width="640" height="480" className="cameraFrame" src={this.state.videoSrc} autoPlay="true"
        ref={(input) => { this.videoElement = input; }}></video>);
    return (
      <div>
        {video}
      </div>
    );
  }
}
{% endhighlight %}

Ok, now we have our video element displaying a stream captured from the camera. Now lets draw an image from this element.

## Drawing Image from Video

We will use a `canvas` element to draw and image from video stream. To achieve that we need to pass video element to the canvas.

{% highlight javascript  %}
class Photo extends Component{
    render(){
        setTimeout(() => {
            if (!this.props.isOpen) {
                return;
            }
            const context = this.canvasElement.getContext('2d');
            context.drawImage(this.props.video, 0, 0, 640, 480);
        });
        const canvasEl = (<canvas id="canvas" width="640" height="480" className="photoCard" ref={(input) => this.canvasElement = canvas} />)
        return (
            <div className="content">
                {canvasEl}
            </div>);
    }
}
{% endhighlight %}

Here timeout is needed to let component render first and then draw an image on the canvas element from video element passed through `video` property of the Photo component. Without timeout there either will not be any canvas element or you will draw on the canvas which will be destroyed once current render finished.

## Conclusion

Using this two components you can allow user to take photos, preview the photo which was just taken and then perform any other actions on them. For instance, you can send them to the server for processing, storage, analysis, etc. So, we learnt how to get stream video on the web page from device camera. Then we learnt how to draw and image on the canvas from the video that was streamed.

[DavidWalsh_Blog]:https://davidwalsh.name/browser-camera
[refs]:https://facebook.github.io/react/docs/refs-and-the-dom.html