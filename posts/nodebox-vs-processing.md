# Nodebox vs Processing

*<sub>Originally published: Sat, 06 Jul 2013 to https://runningwithdata.com/post/54781756667</sub>*

**TL;DR: Nodebox-OpenGL gives you the sugar and power of Python and the ease of Processing, but with a significant speed penalty. The good news is: there’s lots of low-hanging fruit. Let’s submit some diffs!**

[Lynn Cherny](https://twitter.com/arnicas)’s talk, Data Visualization with Nodebox ([Slides](http://www.slideshare.net/arnicas/nodebox-for-data-visualization-17766643), [Video](http://vimeo.com/63270085)) made an excellent case for using [Nodebox](http://www.cityinabottle.org/nodebox/) as a framework for creative data visualization instead of [Processing](http://processing.org/). Nodebox lets you code in Python instead of Java. And although Nodebox lacks Processing’s vibrant community of developers and [rich ecosystem](http://www.processing.org/reference/libraries/) (including [geomerative](http://www.ricardmarxer.com/geomerative/) and [toxiclibs](http://toxiclibs.org/)), Nodebox comes with a lot built-in ([flocking, particle systems, graphs](http://www.cityinabottle.org/nodebox/physics/)), plus easy access to all the goodies you could ever want in Python.  

So, I decided to give it a whirl and installed [Nodebox-OpenGL](https://github.com/nodebox/nodebox-opengl) (hereafter referred to as “Nodebox”). If you have mercurial, git, and pip installed, it’s not too bad (on OSX, [you need to install pyglet from HEAD](http://twistedpairdevelopment.wordpress.com/2012/02/21/installing-pyglet-in-mac-os-x/) because version 1.2, which switches from using Carbon to Quartz, has not yet been released):  

```
    pip install hg+https://pyglet.googlecode.com/hg/
    pip install git+https://github.com/nodebox/nodebox-opengl
```    
    
Once that was out of the way, I decided the most instructive thing to do was to port an existing Processing sketch to Nodebox. BoxyLady2, [a 72-line sketch](https://gist.github.com/jsundram/1671003) by [analogpixel](http://www.analogpixel.org/) became [20 lines shorter](https://gist.github.com/jsundram/5332259) (yay!), but also **11x slower** (.375 [fps](http://en.wikipedia.org/wiki/Frame_rate) for NodeBox, 4.4 fps for Processing v2.0b9). That’s a hell of a performance price to pay.  

My rendered output is [here](http://viz.runningwithdata.com/mosaic/jsundram.gif") (I used ImageMagick to stitch the frames together &ndash; it’s a ~9 MB animated gif)</p> 

[
    ![boxy jsundram](https://lh3.googleusercontent.com/D0MDnAb3MWsByV0fZ6-wzQsXStNczqgPSKXRi5V1GKF-n7rNVxZMpnw34Jdr71lTCD2UsQgjBVCgQI6i5OHcfeYP3QyAytXE5zbBCjQAPOmzG52VY-9PQN9HMw)
](http://viz.runningwithdata.com/mosaic/jsundram.gif)


*[click the photo above to open the animation](http://viz.runningwithdata.com/mosaic/jsundram.gif)*

Despite its performance problems, Nodebox does have a primitive that Processing is lacking however, the [`star`](http://nodebox.net/code/index.php/Reference_%7C_star()). Interestingly, it was implemented using an inefficient intermediate class (`BezierPath`) instead of directly in OpenGL, like the other primitives. Rewriting star in OpenGL (along with some cute tricks), I was able to commit [`fast_star`](https://github.com/jsundram/nodebox-opengl/commit/87e66f57c7fea0520a21b68576f6472ce5097daf), a function that runs 23x faster. So, of course, I made [this](http://viz.runningwithdata.com/mosaic/jsundram_stars_smaller.gif).  

[
    ![starry jsundram](https://lh5.googleusercontent.com/wnbobcfRKiXjOrldse5I1q-02brVTfU_ie_aHOw1WRsRR9FAXBReanECiwpX1FfWg901Fe1IeBg1uL-ODgrh45teKWLDOaAyeDm1T-z1Yh686zYQCXZnXn5jng)
](http://viz.runningwithdata.com/mosaic/jsundram_stars_smaller.gif)

*[click the photo above to open the animation](http://viz.runningwithdata.com/mosaic/jsundram_stars_smaller.gif)*    

And, as advertised earlier, Python’s many great libraries are easily callable from Nodebox. I decided to make a quick project, [`starry.py`](https://gist.github.com/jsundram/5441782#file-starry-py) with `fast_star` and [scikit-image](http://scikit-image.org). It barely scratches the surface of what can be done, but it’s a fun demo.  

Out of curiosity, I also ported the sketch to [processing.js](https://github.com/processing-js/processing-js) (which was pretty straightforward); you can see the result [here](http://bl.ocks.org/jsundram/5605982). I was expecting processing.js to be similarly slow &ndash; how could you expect javascript to keep up with the mighty JVM? I was wrong; the processing.js sketch was the fastest of all, averaging 5.3 fps, or **14x faster than Nodebox**! Admittedly, the code does less work; it doesn’t save any images to disk. (On a side note, the code is easily extended to write out an animated gif using [gif.js](http://jnordberg.github.io/gif.js/)) Removing the image-saving code from Processing (Java) yields a blistering 27.6 fps, while removing it from Nodebox makes almost no difference.  

So why is Nodebox so much slower? I took a look at [`context.py`](https://github.com/nodebox/nodebox-opengl/blob/master/nodebox/graphics/context.py), the file where all the drawing happens. I was able to increase the performance of all the drawing primitives (`line`, `rect`, `triangle`, and `elllipse`) by taking advantage of some specific OpenGL instructions. [Performance improvements](https://github.com/nodebox/nodebox-opengl/commits/master/nodebox/graphics/context.py?author=jsundram) ranged from 12 - 50% &mdash; still not enough to make NodeBox competitive with Processing, when it comes to rendering speed; with the improved code, BoxyLady2.py still only runs at .6 fps, still about 8x slower than javascript.  

Perhaps it’s the [JIT](http://en.wikipedia.org/wiki/Just-in-time_compilation) in JavaScript that makes the difference? To find out, I installed [PyPy](http://pypy.org/index.html), an implementation of Python that uses a JIT. After a slight [rewrite](https://gist.github.com/jsundram/5618041) to avoid using [`numpy`](http://www.numpy.org/), I was able to get 2.7 fps &mdash; about half as fast as processing.js, and over 4x as fast as my existing Python implementation (Apple-supplied Python 2.7.1). So if you’ve got frame rate problems in Nodebox, PyPy may very well be your answer.  

Even with the JIT, and some OpenGL improvements, Nodebox lags behind both Processing implementations. Perhaps they employ occlusion culling or other tricks to avoid doing unnecessary work. If you’re a committer on Processing.org, please take a look at Nodebox and tell us what we could be doing better.  

### Rendering Speeds in frames per second (FPS) 

| Nodebox </br> <sub>(v0.7 stock)</sub> | Nodebox </br> <sub>(latest)</sub> | Nodebox </br> <sub>(latest + PyPy)</sub> | Processing | Processing </br> <sub>(no image saving)</sub> | Processing.js | 
|------|----|-----|-----|------|-----| 
| .375 | .6 | 2.7 | 4.4 | 27.6 | 5.3 |


Long story short, Nodebox has a lot to like, but has a long way to go before it can compete with Processing in terms of community and performance. But that means making incremental improvements is really easy. Go pull out your profiler &mdash;[@huyng](https://twitter.com/huyng) wrote [a great post on profiling Python](http://www.huyng.com/posts/python-performance-analysis/) &mdash; and submit a pull request!  
