---
layout: post
title: 'music21, pandas and condensing sequential data'
published: true
---
_[music21](http://web.mit.edu/music21/), developed by Michael Scott Cuthbert, is an extensively featured and well maintained Python package for computational music theory. Lately, I've been using its highly useful musicxml parsing capability and model of notated music in tandem with [pandas](https://pandas.pydata.org/) DataFrames. I wanted to share a trick I put together for condensing rows of the dataset while keeping the sequential order intact._

The functionality of the music21 package is built on top of the [`Stream`](https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html) data structure, which allows musical material to be stored in a nested forward-linked tree structure. For my purposes I wanted a data structure with more random access features and extensive grouping and filtering capabilities than `Stream`. I was happy sacrificing some of the finely modeled aspects of the music21 ecosystem, holding onto just the attributes I needed for my pipeline and storing them in a pandas DataFrame. In this post I will demonstrate a cool trick for handling sequential data in pandas: combining groups of adjacent rows with `groupby`, while keeping the order of the DataFrame intact.

## Constructing the DataFrame

Let's take a musical score, represented as a `Stream` object, and read the data we need into a DataFrame. First we convert each music21 object we encounter into a row of our DataFrame using this function.

{%gist b2b396905e467c7d365f52980838a9ca %}

Next we iterate over our score and populate our DataFrame with the collected rows. As demonstrated in [this stack overflow answer](https://stackoverflow.com/a/47979665), DataFrames can be constructed substantially faster from a list of dictionaries than from sequential `append` operations. 

{%gist 1368700b66b02ae19557fbf14e22505d %}

## Goal: Merging Adjacent Rows of the Same Type

For my application, I require that a list of consecutive rests (notated silences) be lumped together as a _single_ rest with accumulated duration. All other rows of the dataset must be kept intact, as shown below:


### Before Merging Adjacent Rests

![Before merging]({{ site.baseurl }}/images/rests_before_merging.png)

_An excerpted view of the dataset before adjacent rests are merged together._


### After Merging Adjacent Rests

![After merging]({{ site.baseurl }}/images/rests_after_merging.png)

_The non-rest rows are left alone. Only the rests in adjacent rows are merged together with durations summed. The one adjacent pair of rests remaining in the above view lies at the boundary of two 'Parts', of the Violin and the Cello, so they should not be merged._

## Implementation

pandas DataFrames have an attribute method `groupby`, which can be used to ["split-apply-combine"](https://pandas.pydata.org/pandas-docs/stable/user_guide/groupby.html). This `groupby` method would work out-of-the-box if we wanted to merge _all_ rests into a single row, but we need to be a little trickier to capture only adjacent `Rest`-Type rows and leave other row types intact - all while maintaining the order of the rows.

Here's the function that does it!

{%gist 00f30d06d4e14da581c94a59c5f5f243 %}

### Key Tricks

The list comprehension beginning on line 14 (courtesy of this stack overflow answer)...

{%gist b61f4564868599990997309c12468dbf %}

... divvies up all the rest indices into groups of consecutive indices. `itertools.groupby` sets boundaries between groups where its callable parameter (_here, a lambda_) changes value. The rest of the syntax is some finagling to get the output into the form of a list of lists.

When called on any index in a run of adjacent rests, `get_initial_rest` is defined to return the first index in the run. When called on the index of any other row in the dataset, it just returns the same index. Hence `get_initial_rest` is used to map the indices of the input DataFrame to the indices of the output DataFrame, keeping the order of rows intact when runs of rests get combined. We use `get_initial_rest` as the labeling function that `pandas.DataFrame.groupby` can use to generate groups.

Finally, we define an aggregation function `agg_func` for groupby that sums 'Duration' entries and takes the first row's element for all other columns. Note that this aggregation function does nothing to single-row groups, and does what we want for groups of rests!

