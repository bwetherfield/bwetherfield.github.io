---
layout: post
title: 'pandas, playing along with music21!'
published: true
---
_[music21](http://web.mit.edu/music21/), developed by Michael Scott Cuthbert, is an extensively featured and well maintained Python package for computational music theory. Lately, I've been using its highly useful musicxml parsing capability and model of notated music in tandem with in tandem with the power of pandas DataFrames._

The functionality of the music21 package is built on top of the [`Stream`](https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html) data structure, which allows musical material to be stored in a nested forward-linked tree structure. It is used very powerfully in the library and its broader adoption. 

For my purposes I wanted a data structure with more random access features and extensive grouping and filtering capabilities. I am okay sacrificing some of the musical modeling of the music21 ecosystem, holding onto just the data attributes I need for the project I'm working on. I wanted to show a cool data manipulation that I implemented using `pandas` that would have been harder to implement with music21 on its own.


Let's take a musical score, represented as a `music21.stream.Stream`, and read the data we need into a dataframe. First we convert each music21 object we encounter into a row of our dataframe using this function.

{%gist b2b396905e467c7d365f52980838a9ca %}

Next we iterate over our score and populate our dataframe with the collected rows. As demonstrated in [this stack overflow answer](https://stackoverflow.com/a/47979665), dataframes can be constructed substantially faster from a list of dictionaries than from sequential append operations. 

{%gist 1368700b66b02ae19557fbf14e22505d %}

_I made use of a couple great music21 funcionalities above. The `flat` attribute of `Stream` is a version of the object without any nesting. By calling `stripTies()`, a much more complex function, we combine notes that are tied into a single longer note with the combined duration of the tied notes (whether or not the resulting note can actually be represented in notation that way)._  

![_config.yml]({{ site.baseurl }}/images/untied_tied.png)

_Visual rendering of the same section of music before and after calling `stripTies`. The tie is removed and the connected notes fused into a single note._

Suppose we wanted to do something similar with rests (notated silences), combining contiguous rests into single rests of the same total duration. This operation can be done pretty neatly to the dataframe.

{% highlight Python %}
rests = df[df['Type'] == note.Rest]
{% endhighlight %}

{% highlight Python %}
parts_names = rests['Part Name'].unique()
    rest_runs_in_parts = []
    for part_name in parts_names:
        rest_idx = rests[rests['Part Name'] == part_name].index
        rest_runs_in_part = [list(map(itemgetter(1), g)) for _, g in
                             groupby(enumerate(rest_idx),
                                     lambda ix: ix[1] - ix[0])]
        rest_runs_in_parts.append(rest_runs_in_part)
    rest_runs = reduce(add, rest_runs_in_parts)
{% endhighlight %}

{% highlight Python %}
initial_rest_lookup = {}
for nums in rest_runs:
    initial_rest_lookup.update(dict.fromkeys(nums, nums[0]))

def get_initial_rest(k):
    return initial_rest_lookup.get(k, k)
{% endhighlight %}

{% highlight Python %}
agg_func = dict.fromkeys(df, 'first')
agg_func.update({'Duration': 'sum'})
{% endhighlight %}

{% highlight Python %}
df.groupby(get_initial_rest, axis=0).agg(agg_func)
{% endhighlight %}

{%gist 00f30d06d4e14da581c94a59c5f5f243 %}
