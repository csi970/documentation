!SLIDE
~~~SECTION:notes~~~
Introduce, thanks for coming. We did an independent study this semester with Dr. Kussmaul on the subject of determining how hard it would relatively be to play various pieces of music by examining the sheet music.
~~~ENDSECTION~~~

# Determing Music Difficulty
## Ben Burwell & Andrew Trautmann
## Muhlenberg College



!SLIDE incremental bullets
~~~SECTION:notes~~~
The goals we had were to determine the difficulty of a piece by giving it a numeric score, and to present that information in a way that would be helpful to people looking for music to play.
~~~ENDSECTION~~~

# Goals

* Determine difficulty
* Present in a useful way



!SLIDE incremental

# 2-Minute Music 101

* Key
    * Accidentals
* Time signature



!SLIDE incremental
~~~SECTION:notes~~~
There are several common music notation programs, including Finale and Sibelius which are proprietary, and MuseScore, which is Free/Libre/Open Source Software. MuseScore also hosts MuseScore.com, which is a repository of scores that software users create & upload.

With regard to file formats, the proprietary notation softwares each have their own. There are also audio-focused formats like MIDI that describe the sound more than the notation, so a lot of information and metadata can be lost. There's also a format called MusicXML that is an open standard for describing music in XML
~~~ENDSECTION~~~

# Music in Software

* Notation software
    * Finale, Sibelius, etc...
    * MuseScore (F/LOSS!)
        * [musescore.com](http://musescore.com/)
* Formats
    * Proprietary (`.mus`, `.sib`, ...)
    * Audio-focused (`.midi`)
    * MusicXML (yay open standards!)



!SLIDE incremental
~~~SECTION:notes~~~
Our initial goal was to "make something do something" — a quick way to do that is with Node.js, a JavaScript environment based on the V8 engine from the Chromium project. Huge adoptation, especially for web applications. Lots of packages available via the Node Package Manager.

We also used Github for project management & issue tracking, and ended up having several repositories for various aspects.
~~~ENDSECTION~~~

# Project

* Node.js
    * Runs Javascript on Chromium's V8 engine
    * Huge adoption
    * Lots of packages available via `npm`
* Github for project management



!SLIDE
~~~SECTION:notes~~~
Our initial "make it do something" script opened a MusicXML file and imported it to an object-oriented model.
~~~ENDSECTION~~~

# `v0`

    @@@ XML
    <score-partwise>
        <part id="P1">
            <measure number="1">
                <attributes>
    ...



!SLIDE commandline incremental
~~~SECTION:notes~~~
It would output something like this:
~~~ENDSECTION~~~

    $ node app.js
    Parts: 2
    -----------------------------------
    Measures: 20
    Notes: 80
    Rests: 0
    Key Changes: 4
    Time Changes: 2
    -----------------------------------
    Measures: 20
    Notes: 40
    Rests: 40
    Key Changes: 0
    Time Changes: 2
    -----------------------------------



!SLIDE incremental small
~~~SECTION:notes~~~
Our next task was to compute some sort of difficulty score. We initially predicted that the difficulty would be based on a combination of several indicators, but we needed to know what they were and how to weight them.
~~~ENDSECTION~~~

# How difficult is it?

* Time signature
* Key
* Notes per measure
* Notes per chord
* Accidentals
* Grace notes/ornamentation
* Range
* Distance between successive notes
* Tuplets



!SLIDE
~~~SECTION:notes~~~
We looked at some previously completed research, including some from the Computing Music project at McGill.
~~~ENDSECTION~~~

# Previous Research

* Computing Music at McGill
    * String Fingering Project



!SLIDE incremental
~~~SECTION:notes~~~
We also tried to find existing scoring methods. It turns out that much of the time, music teachers assign pieces to students based on their past experience and judgement. Additionally, music publishers often include ratings in books aimed toward students. There are also online databases of pieces and their ratings online.
~~~ENDSECTION~~~

# Existing Scoring Methods

* Music teachers use their judgement & experience
* Music book publishers assign ratings
* Online databases (e.g. [pianostreet.com](http://www.pianostreet.com/piano_music/download_1/sheet_1.php))
    * Where do the ratings come from???

!SLIDE incremental
~~~SECTION:notes~~~
One of the things we noticed at this point was that the difficulty of a piece might vary greatly depending on the instrument. You can play lots of notes on a cello that just don't exist on a violin. More subtly, though, there can be a significant variation in how hard it is to play a sequence of notes based on differing instrument morphologies. As a result, we selected indicators that would not be impacted by the instrument.
~~~ENDSECTION~~~

# Differences Between Instruments

* Difficulty can vary greatly between instruments
* It is possible to play many things on a cello that are impossible on a violin
* Instrument morphologies can affect difficulty



!SLIDE incremental
~~~SECTION:notes~~~
Another thing we considered was the impact of the length of the piece. We decided to compute all of our indicators on an averaged per-measure basis so that we could compare the difficulty of pieces of differing length. This also allows for sub-part analysis, where we can compare relative difficulty between sections of the piece.
~~~ENDSECTION~~~

# Length

* Indicators calculated per-measure
* Also allows sub-part analysis



!SLIDE incremental
~~~SECTION:notes~~~
Our algorithm is currently farily simplistic. It calculates a score based on the deviation from "normal" of a set of statistics generated by the parser and is based on the following indicators:
~~~ENDSECTION~~~

# The Algorithm

* Based on...
    * Chords per measure
    * Accidentals per measure
    * Key changes per part
    * Time signature changes per part
    * Range



!SLIDE
# Goal #2
## Make it useable



!SLIDE incremental
~~~SECTION:notes~~~
One way we thought of to make it useful to people was to create a browser extension that would activate on certain sites that had MusicXML files, such as MuseScore.com. Currently, it works with Chrome, Firefox, and Safari.
~~~ENDSECTION~~~

# A Browser Extension

* Allows people to quickly determine difficulty while browsing.
* Chrome, Firefox, Safari



!SLIDE incremental
~~~SECTION:notes~~~
To facilitate the extension, we decided to implement server-side processing for the scores. This allows us to leverage caching and extensibility, and is provided in the form of a JSON API.
~~~ENDSECTION~~~

# Backend

* Server-side processing
    * Caching
    * Extensible
* JSON API



!SLIDE incremental
~~~SECTION:notes~~~
We initially decided to focus on MuseScore.com due to its large collection of MusicXML files and availability of a data API that allows lookup of some score metadata.

We registered an API key with MuseScore, which gave us public access to MuseScore data.
~~~ENDSECTION~~~

# MuseScore.com API

* Register for API key by emailing `api@musescore.com`
* Access to public MuseScore.com data through a JSON API



!SLIDE incremental small
~~~SECTION:notes~~~
Our next step was to make our own API. We used an Actionhero API deployed to Heroku. We also used MongoDB to cache difficulty computations, which generally take a few seconds.
~~~ENDSECTION~~~

# <img src="http://www.actionherojs.com/img/logo/actionhero_400.png" height="70"> Actionhero.js



* Use the MuseScore `resolve` method to get JSON data about the score
* Check to see if we have the latest version in our cache
    * If it is, just return that document
* Download the compressed MXL file and unzip it.
* Process the MusicXML and generate difficulty stats
* Cache the statistics and return the document



!SLIDE small smaller
~~~SECTION:notes~~~
Here's an example of what our API will return
~~~ENDSECTION~~~
    
    @@@ JavaScript
    {
      "id": 170165,
      "vid": 237105,
      "uri": "http://api.musescore.com/services/rest/score/170165",
      "permalink": "http://musescore.com/score/170165",
      "title": "The Impossible",
      "description": "",
      "lastModified": 1394661085,
      "parts": [
        {
          "numRests": 391,
          "numChords": 578,
          "numNotes": 934,
          "numAccidentals": 3,
          "numGraceNotes": 0,
          "numMeasures": 50,
          "keyChanges": 0,
          "timeChanges": 0,
          "totalSound": 42.5,
          "totalRest": 153.5,
          "difficulty": 1.6953333333333334,
          "partId": "P1",
          "instrument": "B Trumpet",
          "partName": "B Trumpet",
          "range": {
            "minPitch": "D-1",
            "maxPitch": "G9"
          },
          "timeSigUsage": [],
          "keyUsage": []
        }
      ]
    }



!SLIDE
~~~SECTION:notes~~~
This is used by the browser extensions to display something like this.
~~~ENDSECTION~~~

# Chrome Extension

<img src="/file/_files/share/chrome.png" width="700">



!SLIDE incremental bullets
~~~SECTION:notes~~~
As far as publishing our work, all of our code is available on Github. We have also published an npm package that allows developers to easily analyze a MusicXML score.
~~~ENDSECTION~~~

# Publishing

* ![Github](/file/_files/share/github.png)
* ![npm](/file/_files/share/npm.png)



!SLIDE commandline incremental
~~~SECTION:notes~~~
We decided to publish it on npm to allow the analysis to be easily packaged for use in our Web API. This keeps everything nice and modular. You can install it easily...
~~~ENDSECTION~~~

    $ npm install music-analysis
    music-analysis@0.1.7 node_modules/music-analysis
    ├── numbers@0.5.0
    ├── node-zip@1.1.0 (jszip@2.1.1)
    └── xml2json@0.5.1 (node-expat@2.3.3)



!SLIDE smaller
~~~SECTION:notes~~~
...and use it in your code.
~~~ENDSECTION~~~

# Coffeescript Example!!!

(because it's better than JavaScript)

    @@@ Coffeescript
    music = require 'music-analysis'

    music.parseMXLFile 'song.mxl', (score) ->
        score.parts.forEach (part) ->
            stats = part.getRawStats()
            console.log "This piece has #{stats.numMeasures} measures."



!SLIDE incremental
~~~SECTION:notes~~~
There are a number of ways we'd like to improve our difficulty algorithm
~~~ENDSECTION~~~

# Algorithm Improvements

* Additional indicators
    * Distance between successive notes
    * Complex rhythm analysis
* Compare and tune to existing datasets



!SLIDE incremental
~~~SECTION:notes~~~
In addition to improving the difficulty algorithm, we'd like to add features to the web service. Specifically, we'd like to make the extension active on other music file repository sites, as well as create a place for musicians to upload their own music files directly for analysis rather than having to upload them somewhere else first.
~~~ENDSECTION~~~

# Web Service Improvements

* Add other music sites
* Create upload page for direct analysis



!SLIDE

# Demo :)



!SLIDE

# Questions?



!SLIDE bullets

# Thanks!

* https://github.com/csi970