---
title: Earworm and Capsule
redirect_from: 
    - post/597154309
    - post/597154309/earworm-capsule
---
# Earworm and Capsule

---

**NOTE**: The Echo Nest was [acquired by Spotify](https://techcrunch.com/2014/03/07/spotify-echo-nest-100m/) in 2014; most but not all API references are now part of the [Spotify API](https://developer.spotify.com/documentation/web-api/reference/#endpoint-get-audio-analysis). Unfortunately, the audio links are no longer functional and cloud.py has since been deprecated..

---

Over the past month, [Tristan](http://web.media.mit.edu/~tristan/) and I have been hard at work adding some new features to [The Echo Nest Remix API](https://echonest.github.io/remix/index.html), which is now at version 1.3. Here's what's new:

* **cloud.py** - functionality to search for analyzed tracks to remix. No need to have your own audio.
* **[pydirac](https://github.com/echonest/remix/tree/master/external/pydirac225)** - a new, great-sounding time-stretcher, which is stereo, and sample accurate. It's a Python wrapper around a C module that makes use of the [Dirac LE](https://web.archive.org/web/20091217100027/http://www.dspdimension.com/technology-licensing/dirac2/) library.
* **action.py** and **cAction** - crossfade, crossmatch, fadein, fadeout, and jump, with C implementations of core functionality for speed.  

We've developed two cool new examples to show off the new functionality: Earworm and Capsule.

## Earworm

[Earworm](https://github.com/echonest/remix/tree/master/examples/earworm) (thanks to [Jonathan Feinberg](http://mrfeinberg.com/) for the name) makes it possible to extend or shrink a song to any length you might desire, without changing the tempo. It does this by constructing a network graph of the piece, using The Echo Nest's analysis data. Each node in the graph is a beat in the song, and an edge exists between two nodes if the two beats, and the several beats that follow them, sound similar (close in timbre and pitch).
   
![Song Graph](https://user-images.githubusercontent.com/150536/128895903-69b6554d-b5b4-4420-97ae-b9d53f04fe88.png)  
*One possible rendering of a song graph.*

The graph shows us where we can make seamless transitions between different parts of the song. Stretching (or shrinking) the song is then just a matter of minimizing the number of "loop" points to reach a requested duration.
   
Tristan took [If I Ever Feel Better](https://open.spotify.com/track/3AA8xNhDC0MpqwkGX3EP5V) by Phoenix and made *If I Ever Feel Longer*, seamless 10 and 60-minute renditions of that track using earworm. He also made a track I call *I Can't Get Any Shorter*, the shortest path through the song with reasonable transitions (about 25% as long as the original). And if you just can't get enough of **If I Ever Feel Better**, you can have *If I Ever Feel Better Forever*, a 200-second version of the song that ends where it starts. If you loop it, you can listen to the track nonstop, seamlessly, forever.
   
It's easy to make your own earworm. Install [pyechonest](https://github.com/echonest/pyechonest), install [remix](https://github.com/echonest/remix), and cd to the earworm example:
   

```python
> python earworm.py my_music.mp3
```

Wait a moment for the analysis to download, and before you know it, you'll have a 10-minute version of whatever song you supplied. What you do next is up to you &hellip;


## Capsule

[Capsule](https://github.com/echonest/remix-examples/tree/master/capsule) takes a list of tracks and automatically arranges and mixes them together. You can specify the transition time, and how long to hear each song. First, it searches for the best location where to make a transition and aligns beats. Because the songs may have different tempos, it uses one of the new actions, [`Crossmatch`](https://github.com/echonest/remix/blob/master/src/echonest/remix/action.py#L219), which simultaneously cross-fades and beat-matches audio in the transitions. Crossmatch uses pydirac, the new time stretcher, to smoothly speed up and slow down the two song's tempos. Crossmatch is an incredibly powerful tool for making great-sounding transitions.  So if you pass in two songs, with the default parameters, you'll get 8 seconds of the first one, an 8 second crossmatch, 8 seconds of the second song, and a 6 second fade-out. 

Hear it transition between two Bob Marley songs: *Jamming* and *Everything is Gonna Be Alright*.

The capsules render pretty quickly, due to cAction, the Python C Extension that handles some of the most computationally intensive code. So why stop at two songs? You can pass as many as you like. 

Try it out, and post your results. I can't wait to hear them. I'll leave you with some ideas to get those creative juices flowing:

Bonus Earworms: 
* The Rolling Stones for 1 hour
* An Hour with Brad Mehldau

Bonus Capsules:
* The Parliament to The Gotan Project to Michael Jackson to Rihanna
* Sade to Sting

---
*<sub>Originally published: Fri, 14 May 2010 to [https://runningwithdata.tumblr.com/post/597154309](https://runningwithdata.tumblr.com/post/597154309)</sub>*

