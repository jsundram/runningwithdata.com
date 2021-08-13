---
title: Songbird's first music visualizer: SongbirdVis
redirect_from: 
    - post/655428696
    - post/655428696/songbirdvis
---
# Songbird's first music visualizer: SongbirdVis

---

**NOTE**: The Echo Nest was [acquired by Spotify](https://techcrunch.com/2014/03/07/spotify-echo-nest-100m/) in 2014; most but not all API references are now part of the [Spotify API](https://developer.spotify.com/documentation/web-api/reference/#endpoint-get-audio-analysis). [Songbird](https://en.wikipedia.org/wiki/Songbird_(software)) was discontinued in 2013.

---


For San Francisco [Music Hack Day](http://new.musichackday.org/), I teamed up with [Steven Lloyd](http://repeatingbeats.com/) to integrate a music visualizer into [Songbird](https://en.wikipedia.org/wiki/Songbird_(software)). As far as I know, this is the first music visualization for Songbird. Since we didn't have access to the raw audio through Songbird, we used [The Echo Nest](http://developer.echonest.com/)'s analysis data, which gives high-level, musically meaningful information to display. SongbirdVis represents pitch, timbre, loudness, rhythmic, and structural information from the analysis data, and synchronizes it with the audio. **In addition to showing information about the currently playing track, SongbirdVis is interactive. You can click on part of the visualization to hear the music at that point. You can also click and drag to select a part of the visualization to zoom in on.**

## Analyze

To get analysis data for a track, all you need is a [free Echo Nest API key](http://developer.echonest.com/account/register/). Instead of uploading the track for analysis, we use the new [v4 beta search API](http://beta.developer.echonest.com/song.html#search) to see if The Echo Nest has already analyzed the currently selected track. When the API is updated sometime in the next month, we'll update the code to upload the track, or search by the track's MD5 checksum.  

The analysis data contains a lot of deep and musically interesting information about a piece of music. Bars, beats, structural sections &hellip; there's pitch and timbre data for every perceptual event in the track. The trick for a developer is to decide how to visually represent that.  

I've been thinking a lot about those issues lately as a part of visualizer.fm, a project to synchronize [HTML 5 audio](http://html5doctor.com/native-audio-in-the-browser/) playback of music with visualizations of Echo Nest Analysis data using [processing.js](https://github.com/processing-js/processing-js) (more about visualizer.fm in a separate post). I decided to port the *diagnostic* visualizer to work in Songbird. I thought it was a good choice since it shows the whole song at once, so it is good for both viewing the analysis for a track, and for use during playback.  

## The Visualization

[
    ![SongbirdVis](http://github.com/jsundram/songbirdvis/raw/b3ad7237652bb4b1618b934d8a3277109ececfa8/screenshots/SongbirdVis.png)
](http://github.com/jsundram/songbirdvis/raw/b3ad7237652bb4b1618b934d8a3277109ececfa8/screenshots/SongbirdVis.png)
*Songbird displaying SongbirdVis for the track &ldquo;Dancing Queen&rdquo; by ABBA.*


[
    ![SongbirdVis (zoomed)](http://github.com/jsundram/songbirdvis/raw/db378394133b08738082f0b29d7b5eaa0519cd2e/screenshots/zoomed.png)
](http://github.com/jsundram/songbirdvis/raw/db378394133b08738082f0b29d7b5eaa0519cd2e/screenshots/zoomed.png)
*Zooming in on the first section.*

The timbre, pitch, and loudness features are all in terms of *segments*. A [segment](http://web.media.mit.edu/~tristan/phd/dissertation/chapter3.html#x1-360003.4) corresponds to a perceptual event (e.g. guitar note, drum hit) in a song.   

Working from top to bottom, here's what SongbirdVis displays:

### timbre
![timbre](http://github.com/jsundram/songbirdvis/raw/db378394133b08738082f0b29d7b5eaa0519cd2e/screenshots/timbre.png)
The timbre display shows the the 12-dimensional timbre vector for each segment. Longer segments take up more horizontal space. The timbre vector is colored by interpreting the first 3 dimensions as RGB values.   

### pitch
![pitch](http://github.com/jsundram/songbirdvis/raw/b3ad7237652bb4b1618b934d8a3277109ececfa8/screenshots/chroma.png)
The pitch display shows the 12-dimensional pitch vector for each segment. Each value corresponds to the strength of a pitch at that point. So, if a guitar plays the note G, and there is no other sound, only the bin for G would be colored in. Because of percussive and other non-pitched sounds, we see a lot of color on the pitch display. Some filtering or weighting might be in order to make this display a bit cleaner.  Pitch colors are chosen by taking a note frequency in hertz and finding the color of the corresponding wavelength of light.

### loudness
![loudness curve](http://github.com/jsundram/songbirdvis/raw/db378394133b08738082f0b29d7b5eaa0519cd2e/screenshots/loudness.png)
The loudness curve shows the perceptual loudness (in dB) over the course of the track. The thickness of the curve shows the difference between the loudness at the beginning of a segment (loudness start), and the maximum loudness for that segment. The white line shows the overall loudness for the track. The vertical white lines show where the analysis data has marked "end of fade in" and "start of fade out". 

### meter
![meter](http://github.com/jsundram/songbirdvis/raw/db378394133b08738082f0b29d7b5eaa0519cd2e/screenshots/meter.png)
The meter display shows Bars, Beats, and [Tatums](http://en.wikipedia.org/wiki/Tatum_grid) as blue, red, and white squares, respectively. Because the beats and tatums are so close to each other when fully zoomed out, it looks like they are thick lines.  The blue, red, and white curves show the confidence associated with each of the squares they correspond to. In Dancing Queen, you can see that the confidence associated with beats is a lot higher, on average, than the confidence associated with bars or tatums. The gray vertical bars correspond to sections, higher-level structures in the song, such as chorus and verse. This makes it easy to jump around between chorus and verse.

### Songbird
The [Songbird Developer Tools ](http://addons.songbirdnest.com/addon/68) add-on makes it very easy to create an extension. There's a wizard and everything. Because Songbird is based on Firefox, writing extensions is just a matter of editing some XUL and javascript. No C++ to compile or anything. SongbirdVis is a [Media Page](http://wiki.songbirdnest.com/Developer/Articles/Getting_Started/Building_Media_Views), another view of the current media. We're working with XUL instead of HTML, so although things are familiar, code changes are necessary to get things to work. The visualizer is tied in to Songbird in a couple of ways: 

* Sync - SongbirdVis receives an updated timestamp from Songbird as the track plays, allowing the visualizer to match what is playing.
* Seek - Clicking on SongbirdVis allows you to hear the track at the place you clicked by setting the currently playing position in the track.
* Track changed - When a new track starts playing, SongbirdVis queries the Echo Nest API to display the visualization for the new track.
* Resizing - When the Songbird window is resized, SongbirdVis resizes itself.

### Processing.js
Processing.js is an awesome port of the Processing.org language to javascript. It's not complete, and it's moving fast, but it's a lot of fun to use, and works in the browser without any need for plugins. It's a great framework for quickly developing Echo Nest visualizers, as I've found out in my work on visualizer.fm. Steve took on the heroic task of updating processing.js (which I use to do all of the drawing) to work inside XUL. This allowed me to avoid writing custom code for Songbird; The diagnostic visualizer used in SongbirdVis is written against processing.js, which means it can run anywhere processing.js can.

### Future Work
SongbirdVis needs the Echo Nest to update the v4 search API and track upload API before it can be released for general use. There are also a couple of things that remain to be fixed in our hacked-up copy of processing.js. In the meanwhile, the code is available [on Github](http://github.com/jsundram/songbirdvis/) for your forking pleasure. SF Music Hack day was a great experience, with loads of smart developers doing cool things. You should definitely check out [the full list of other hacks](http://musichackdaysf2010.pbworks.com/Projects). My favorite was Leonard Lin's [Set Summary](http://musichackdaysf2010.pbworks.com/SetSummary). The best part about it for me, was that it uses [Capsule](http://runningwithdata.tumblr.com/post/597154309/earworm-capsule). It's a real joy to see people make cool stuff with code I've developed.

---
*<sub>Originally published: Wed, 02 Jun 2010 to [https://runningwithdata.com/post/655428696](https://runningwithdata.com/post/655428696)</sub>*

