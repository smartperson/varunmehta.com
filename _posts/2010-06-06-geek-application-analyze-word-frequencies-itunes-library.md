---
layout: post
published: true
title: "Geek: Application to analyze word frequencies in your iTunes library"
author: Varun
categories: Technology Personal Music
image: /img/music.jpg
---

You all know I enjoy music a lot. I tend to go through phases with particular genres, but love music of all types. Recently I found myself wondering: what words occur the most frequently in the music that I listen to? I had some guesses, but wanted to do a proper in-depth analysis.

I call this little app "LyricsHistogram." It does the following:

* Uses [ScriptingBridge](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/ScriptingBridgeConcepts/Introduction/Introduction.html) technology from Apple to get the list of tracks you have in iTunes
* Uses [LyrDb](http://www.lyrdb.com/) to fetch a set of lyrics for each song
* Breaks out all of the words in each song and keeps track of the frequency of each one
* Optionally weights the lyrics of each song based on how many times it has been played in iTunes
  * I added this weighting ability after I realized I listed to some songs very frequently and never listened to some of the music in my library
* Outputs word-frequency pairs in comma-separated (CSV) format for detailed analysis using programs like Microsoft Excel

This application is available via my shared Subversion repository [here](http://svn.mehtasw.com/sharedsvn/LyricsHistogram/). If you don't know what that means, then this application is not ready for you to use yet - sorry. Usage help is available on the command-line.

With my machine's speed and network performance I run through my library of ~5400 songs in about 10 minutes. Since this will hit the LyrDb database every time you run it (I haven't added lyrics caching yet) try not to run it frivolously. Please enjoy and experiment with this code. If you want write-access to the repository to contribute, let me know. One day we might be able to make this a really interesting and useful application.

It was really fun to use ScriptingBridge and simple web services to write this app. I've run both weighted and unweighted on my library and the results were very interesting. I'll have another blog post to share what I've seen with my own music library another day.