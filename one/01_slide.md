!SLIDE 
# Determing Music Difficulty
## Ben Burwell & Andrew Trautmann
## Muhlenberg College
.notes Introduce, thanks for coming. We did an independent study this semester with Dr. Kussmaul on the subject of determining how hard it would relatively be to play various pieces of music by examining the sheet music.



!SLIDE
# Goals

.notes The goals we had were to determine the difficulty of a piece by giving it a numeric score, and to present that information in a way that would be helpful to people looking for music to play.

* Determine difficulty
* Present in a useful way



!SLIDE incremental
# 2-Minute Music 101

* Key
    * Accidentals
* Time signature



!SLIDE incremental
# Music in Software

.notes There are several common music notation programs, including Finale and Sibelius which are proprietary, and MuseScore, which is Free/Libre/Open Source Software. MuseScore also hosts MuseScore.com, which is a repository of scores that software users create & upload.


.notes With regard to file formats, the proprietary notation softwares each have their own. There are also audio-focused formats like MIDI that describe the sound more than the notation, so a lot of information and metadata can be lost. There's also a format called MusicXML that is an open standard for describing music in XML
* Notation software
    * Finale, Sibelius, etc...
    * MuseScore (F/LOSS!)
        * [musescore.com](http://musescore.com/)
* Formats
    * Proprietary (`.mus`, `.sib`, ...)
    * Audio-focused (`.midi`)
    * MusicXML (yay open standards!)


!SLIDE incremental
# Project

.notes Our initial goal was to "make something do something" — a quick way to do that is with Node.js, a JavaScript environment based on the V8 engine from the Chromium project. Huge adoptation, especially for web applications. Lots of packages available via the Node Package Manager.
.notes We also used Github for project management & issue tracking, and ended up having several repositories for various aspects.

* Node.js
    * Runs Javascript on Chromium's V8 engine
    * Huge adoption
    * Lots of packages available via `npm`
* Github for project management



!SLIDE
# `v0`

.notes Our initial "make it do something" script opened a MusicXML file and imported it to an object-oriented model.

    @@@ XML
    <score-partwise>
        <part id="P1">
            <measure number="1">
                <attributes>
    ...

!SLIDE commandline incremental
.notes It would output something like this:

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



!SLIDE incremental
# How difficult is it?

.notes Our next task was to compute some sort of difficulty score. We initially predicted that the difficulty would be based on a combination of several indicators, but we needed to know what they were and how to weight them.

* Indicators
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
# Previous Research

.notes We looked at some previously completed research, including some from the Computing Music project at McGill.

* Computing Music at McGill
    * String Fingering Project



!SLIDE incremental
# Existing Scoring Methods

.notes We also tried to find existing scoring methods. It turns out that much of the time, music teachers assign pieces to students based on their past experience and judgement. Additionally, music publishers often include ratings in books aimed toward students. There are also online databases of pieces and their ratings online.

* Music teachers use their judgement & experience
* Music book publishers assign ratings
* Online databases (e.g. [pianostreet.com](http://www.pianostreet.com/piano_music/download_1/sheet_1.php))
    * Where do the ratings come from???

!SLIDE incremental
# Differences Between Instruments

.notes One of the things we noticed at this point was that the difficulty of a piece might vary greatly depending on the instrument. You can play lots of notes on a cello that just don't exist on a violin. More subtly, though, there can be a significant variation in how hard it is to play a sequence of notes based on differing instrument morphologies. As a result, we selected indicators that would not be impacted by the instrument.

* Difficulty can vary greatly between instruments
* It is possible to play many things on a cello that are impossible on a violin
* Instrument morphologies can affect difficulty



!SLIDE incremental
# Length

.notes Another thing we considered was the impact of the length of the piece. We decided to compute all of our indicators on an averaged per-measure basis so that we could compare the difficulty of pieces of differing length. This also allows for sub-part analysis, where we can compare relative difficulty between sections of the piece.

* Indicators calculated per-measure
* Also allows sub-part analysis

!SLIDE incremental
# The Algorithm

.note Our algorithm is currently farily simplistic. It calculates a score based on the deviation from "normal" of a set of statistics generated by the parser and is based on the following indicators:

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
# Algorithm Improvements

.notes There are a number of ways we'd like to improve our difficulty algorithm

* Additional indicators
    * Distance between successive notes
    * Complex rhythm analysis
* Compare and tune to existing datasets
