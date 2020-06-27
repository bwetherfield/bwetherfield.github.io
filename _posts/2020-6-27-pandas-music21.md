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

For my application, I require that a list of consecutive rests (notated silences) be lumped together as a _single_ rest with accumulated duration. For the first step, we extract a DataFrame of all the rests, and look for consecutive rows using `itertools.groupby`. Our intention is to use the `pandas.DataFrame.groupby` method, which .

![Before merging]({{ site.baseurl }}/images/before_merging)

![After merging]({{ site.baseurl }}/images/after_merging)


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
