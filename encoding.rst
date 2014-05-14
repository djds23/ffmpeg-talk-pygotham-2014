:title: A Primer on FFmpeg and Video Compression
:author: Dean Silfen
:description: Some quick words on how to compress video for the internet.
:keywords: presentation, restructuredtext, impress.js, tutorial
:css: main.css

----

A Primer on FFmpeg and Video Compression
========================================

By Dean Silfen
--------------

----

Group of Picture Compression
============================

Make smaller videos by recycling data from past and future frames. 
    
    - Divide the video into 3 different types of frames.
        
        - *I*-frames (intra or key frames) contain all data needed to recreate an image. An I-frame stands alone as a complete image with no data missing.  

        - *P*-frames (predicted frames) reference other P or I frames that come before them. 

        - *B* frames (bi-directional frames) reference other P or I frames within either direction of this frame. 

----

Which codec to chose?
=====================

----

h.264!
======

----

Tools?
======

The swiss army knife of digital video!

FFmpeg: https://www.ffmpeg.org/

----

How do I do "this"?
===================

Googling a particular use case does not yield the best results!

Be careful about copying and pasting a command from the internet!

----

Basic input:
============

Your typical script looks like this:



``ffmpeg -i input.mp4 -c:a copy -c:v copy output.mp4``


The ``-c:a`` and ``-c:v`` stand for audio and videos codecs respectively.


----

Basic input part 2:
===================

:: 

    -v (verbosity)
    -r (framerate)
    -t (duration in seconds)
    -metadata (key=value)
    -frames:v (limit number of frames)
    -an (no audio)
    -vn (no video)


----

How can I get h.264 working?
============================

Libx264 is an excellent encoding/decoding library for h.264 files, and it has bindings with our favorite software!

::

    ffmpeg -i input.mp4 -c:a copy -c:v libx264 output.mp4


----

Lets get CRF-ty
===============

Bandwidth can be expensive, use these methods to trim your bitrate!

Constant Rate Factor compress similar frames a similar amount.

::

    -crf (quantization number between 0 for best quality and 51 for worst)


Multipass encoding creates a reference file for better look ahead data!

::

    -pass 1 (first pass)
    -pass 2 (second pass)


Combine this with ``-b:v (bitrate)`` for a target bitrate to compress video for best results.


----

Presets
=======

Trust in the presets! They are there for a reason!

Take a look at them here:

https://trac.ffmpeg.org/wiki/x264EncodingGuide

``-presets (option)`` will set up your encoding preset

``-tune (option)`` cater to your specific video, animation, film, fastdecode

----


Lets build more complex commands!
=================================

You got a 1 minute video? Lets make it 5mb.

filesize in kilobits / length in seconds = approximate target bitrate

Subtract audio bitrate from result

(5*8192)/60 = ~683

683 - 128 = 555k

----

:: 

    ffmpeg\
    -i showme.mov\
    -an\
    -c:v libx264\
    -preset slow\
    -tune animation\
    -pass 1\
    -b:v 555k\
    -f mp4 /dev/null
    

----

::

    ffmpeg\
    -i input.mp4\
    -c:v libx264\
    -preset slow\
    -b:v 555k\
    -tune animation\
    -pass 2\
    -c:a libfaac\
    -b:a 128k\
    output.mp4 



----

Filters
=======

``-vf`` applies a video filter

``scale:width:height:flags=lanczos`` for effcient resizing

``yadif=0:-1:0`` yet another deinterlacing filter...


----

libx264 bindings vs FFmpeg bindings
===================================

libx264 has more options than FFmpeg has flags!

``-x264opts (key=value:key=value)``


----

Experiment!
============

Inspect your videos using a tool like Mediainfo, and see what your presets did, then tweak accordingly.

http://mediaarea.net/en/MediaInfo

----

Fun use cases
=============

``-v:c prores -profile:v 1`` creates edit friendly prores files!

``-vf scale:612:612:flags=lanczos`` for instagram!

Create a streaming server with ffserver! (Linux only!)

----

More Codecs!
============

Check out libvpx for efficient webm encoding. Playable on android devices!

----

Python?
=======

Subprocess module!

Check out the MoviePy library!
------------------------------

This library handles many of the arguments for you, allowing you to process and edit video straight from Python. 

Guess what it outputs to?

----

Links/References
================

-http://trac.ffmpeg.org/wiki/CompilationGuide

-https://trac.ffmpeg.org/wiki/x264EncodingGuide

-http://www.ffmpeg.org/ffmpeg-all.html

-https://trac.ffmpeg.org/wiki/vpxEncodingGuide

-https://trac.ffmpeg.org/wiki/Streaming%20media%20with%20ffserver

-http://avisynth.org.ru/yadif/yadif.html

-http://documentation.apple.com/en/finalcutpro/usermanual/index.html#chapter=C%26section=12%26tasks=true
