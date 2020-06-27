---
layout: post
title: 'pandas, playing along with music21!'
published: true
---
_[http://web.mit.edu/music21/](music21), developed by Michael Scott Cuthbert, is an extensively featured and well maintained Python package for computational music theory. Of late, I've been using its highly useful musicxml parsing capability and model of notated music in tandem with [pandas](https://pandas.pydata.org/), whose `DataFrame` data structure (conveniently defined somewhere between a database and an array) is a stalwart of my data analysis work._

The functionality of the music21 package is built on top of the [`Stream`](https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html) data structure, which allows musical material to be stored in a nested forward-linked tree structure. It is used very powerfully in the library and its broader adoption. For my purposes I wanted a data structure with more random access features and extensive grouping and filtering capabilities. I am okay sacrificing some of the musical modeling of the music21 ecosystem, holding onto just the data attributes I need for the project I'm working on. I wanted to show a cool data manipulation that I implemented using `pandas` that would have been harder to implement with music21 on its own.

## Step 1

Let's take a musical score, represented as a `music21.stream.Stream`, and read the data we need into a dataframe.

{%gist b2b396905e467c7d365f52980838a9ca %}

![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
