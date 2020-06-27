---
layout: post
title: 'pandas, playing along with music21!'
published: true
---
_[music21](http://web.mit.edu/music21/), developed by Michael Scott Cuthbert, is an extensively featured and well maintained Python package for computational music theory. Lately, I've been using its highly useful musicxml parsing capability and model of notated music in tandem with [pandas](https://pandas.pydata.org/), whose `DataFrame` data structure (conveniently defined somewhere between a database and an array) is a stalwart of my data analysis work._

The functionality of the music21 package is built on top of the [`Stream`](https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html) data structure, which allows musical material to be stored in a nested forward-linked tree structure. It is used very powerfully in the library and its broader adoption. 

For my purposes I wanted a data structure with more random access features and extensive grouping and filtering capabilities. I am okay sacrificing some of the musical modeling of the music21 ecosystem, holding onto just the data attributes I need for the project I'm working on. I wanted to show a cool data manipulation that I implemented using `pandas` that would have been harder to implement with music21 on its own.


Let's take a musical score, represented as a `music21.stream.Stream`, and read the data we need into a dataframe. First we convert each music21 object we encounter into a row of our dataframe using this function.

{%gist b2b396905e467c7d365f52980838a9ca %}

Next we iterate over our score and populate our dataframe with the collected rows. As I learned in [this stack overflow answer](https://stackoverflow.com/a/47979665), dataframes can be constructed substantially faster from a list of dictionaries than from sequential append operations. 

{%gist 1368700b66b02ae19557fbf14e22505d %}

_I made use of a couple great music21 funcionalities above. The `flat` attribute of `Stream` is a version of the object without any nesting. By calling `stripTies()`, a much more complex function, we combine notes that are tied into a single longer note with the combined duration of the tied notes (whether or not the resulting note can actually be represented in notation that way)._  



![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
