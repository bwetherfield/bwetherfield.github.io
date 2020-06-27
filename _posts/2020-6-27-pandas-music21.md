---
layout: post
title: '`work-in-progress` pandas, playing along with music21!'
published: true
---
_[music21](http://web.mit.edu/music21/), developed by Michael Scott Cuthbert, is an extensively featured and well maintained Python package for computational music theory. Lately, I've been using its highly useful musicxml parsing capability and model of notated music in tandem with the power of pandas DataFrames._

The functionality of the music21 package is built on top of the [`Stream`](https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html) data structure, which allows musical material to be stored in a nested forward-linked tree structure. It is used very powerfully in the library and its broader adoption by the user community. 

For my purposes I wanted a data structure with more random access features and extensive grouping and filtering capabilities than `Stream`. I was happy sacrificing some of the finely modeled aspects of the music21 ecosystem, holding onto just the attributes I needed for my pipeline and storing them in a pandas DataFrame. In this post, I will show a neat transformation to my musical data I was able to do with the help of `pandas` functionality. 

## Putting Together the DataFrame

Let's take a musical score, represented as a `Stream` object, and read the data we need into a dataframe. First we convert each music21 object we encounter into a row of our dataframe using this function.

{%gist b2b396905e467c7d365f52980838a9ca %}

Next we iterate over our score and populate our dataframe with the collected rows. As demonstrated in [this stack overflow answer](https://stackoverflow.com/a/47979665), dataframes can be constructed substantially faster from a list of dictionaries than from sequential append operations. 

{%gist 1368700b66b02ae19557fbf14e22505d %}

## Merging adjacent rows of the same type

For my application, I require that a list of consecutive rests (notated silences) be lumped together as a _single_ rest with accumulated duration. All other rows of the dataset must be kept in tact, as shown below:


### Before merging adjacent rests

![Before merging]({{ site.baseurl }}/images/rests_before_merging.png)

_An excerpted view of the dataset before adjacent rests are merged together._


### After merging adjacent rests

![After merging]({{ site.baseurl }}/images/rests_after_merging.png)

_The non-rest rows are left alone. Only the rests in adjacent rows are merged together with durations summed. The one adjacent pair of rests remaining in the above view lies at the boundary of two 'Parts', of the Violin and the Cello, so they should not be merged._

## Using `pandas.DataFrame.groupby` to group rows only if they are adjacent

{%gist 00f30d06d4e14da581c94a59c5f5f243 %}
