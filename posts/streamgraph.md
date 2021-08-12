# streamgraph.js

*<sub>Originally published: Sun, 02 May 2010 to [https://runningwithdata.com/post/566345323](https://runningwithdata.com/post/566345323)</sub>*


[Streamgraphs](http://leebyron.com/streamgraph/) are cool. They're great at displaying trends in data over time, similar to a stacked graph, but much prettier. The first one I saw was Lee Byron's [Last.fm Listening History](http://www.vislives.com/2011/10/visualizing-lastfm-listening-history.html) graphic, a beautiful poster showing trends in the music he had listened to over the course of two years. The New York Times used an interactive streamgraph (created by [Matthew Bloch](https://twitter.com/grammata) and Shan Carter) to great effect to show box office receipts over 22 years in [The Ebb and Flow of Movies](http://www.nytimes.com/interactive/2008/02/23/movies/20080223_REVENUE_GRAPHIC.html).
   
When Lee Byron &amp; Martin Wattenberg [open-sourced](http://github.com/leebyron/streamgraph_generator) their streamgraph implementation in Java and Processing, I was pretty excited. I've been playing around with [processing.js](https://github.com/processing-js/processing-js) a lot lately, so I decided to port their code. 
   
I've posted the code [on github](https://github.com/jsundram/streamgraph.js). The algorithms are much the same as Byron &amp; Wattenberg's, but I've added code to make the graphs more interactive and easily configurable. 
   
The major things I added are:
* **Dynamic sizing** (sized to fit the Canvas element it's on).
* **Settings.** These can be dynamically changed, and easily set outside of the streamgraph code.
* **Zoom.** You can click and drag to zoom in, and press Escape to zoom out.
* **Hover.** The selected layer changes color, and the active layer name and data point is displayed at the top of the screen.
* **Color.** Another color option that offers a bit more variation.
* **Examples** (including the original beautiful examples).

Streamgraph.js doesn't quite have feature parity with the interactive flash streamgraph shown in *The Ebb and Flow of Movies*, but I would like it to get there. I'm waiting for the text handling in processing.js to catch up to what's available in Processing. The processing.js team is moving fast, so I think it will happen soon.
   
Here are some screenshots of streamgraphs.js in action. Full source for these examples is [on github](https://github.com/jsundram/streamgraph.js).
   
![Late Onset, with NYT colors](https://raw.githubusercontent.com/jsundram/streamgraph.js/master/examples/screenshots/late_onset.png)
*Byron &amp; Wattenberg's **Late Onset** example*
   
![Believable](https://raw.githubusercontent.com/jsundram/streamgraph.js/master/examples/screenshots/believable.png)
 *Byron &amp; Wattenberg's **Believable** example, using a more varied color set.*
   
![last.fm listening history, top 20 artists](https://raw.githubusercontent.com/jsundram/streamgraph.js/master/examples/screenshots/lastfm.png)   
*[@reiddraper](http://twitter.com/reiddraper)'s [last.fm](http://www.last.fm/user/PumaReid) listening data for top 20 artists over 2 years.*
   
![Pitch data from part of the Aria from Bach's Goldberg Variations](https://raw.githubusercontent.com/jsundram/streamgraph.js/master/examples/screenshots/aria.png)
  
Pitch data from part of the [Goldberg Variations](https://vimeo.com/2671233), obtained using The Echo Nest's [Track API](https://developer.spotify.com/documentation/web-api/reference/#endpoint-get-audio-analysis)</a>. Note the legend and the custom coloring: a mapping of pitch frequencies onto wavelengths of light.
   
Get the code: [streamgraph.js on github](https://github.com/jsundram/streamgraph.js)
