---
layout: post
title: 'Mountains out of Molehills: a Pullback Construction for numpy'
published: true
---
_At the time of my writing [my undergrad thesis](https://dash.harvard.edu/handle/1/38779539), I bit off more than I could chew computationally. Though my whole intention was to come up with a computational model and algorithm for spelling the notes in a musical score ('choosing sharps and flats'), the math ended up keeping me sufficiently busy. I laid out a theoretical roadmap for implementation, without explicitly doing any programming. In particular, I left myself quite a hefty empirical study to do, in which I would train the algorithm on a large corpus of musical scores. I am now ready to embrace the messiness of real data from real scores with a trusty Python stack. Here I'll talk about a [numpy](https://numpy.org/doc/1.18/index.html) abstraction that keeps coming up._

## Concepts

My undergraduate thesis made frequent use of a "pullback" construction for describing large matrices in terms of smallers ones, by performing an operation on the indices of the large matrix. 

I'll show what I mean by way of an example.

Let us define a small (2x2) matrix as follows:

$$m = \begin{pmatrix}0 & 1 \\\\ 2 & 3\end{pmatrix}$$

We want to define a larger 5x5 matrix. To perform a pullback, we need a map $f: {\mathbf{Z}}_5 \to {\mathbf{Z}}_2$.

Let us define our map as follows.

$$\begin{align}
f: 0 &\mapsto 0 \\\\
f: 1 &\mapsto 0 \\\\
f: 2 &\mapsto 0 \\\\
f: 3 &\mapsto 1 \\\\
f: 4 &\mapsto 1 \\\\
\end{align}$$

Now the pullback $f^{-1}(m)$ is defined as follows. 

$$\begin{align} M &= f^{-1} (m) \\\\

&= \begin{pmatrix} 0 & 0 & 0 & 1 & 1 \\\\ 
                   0 & 0 & 0 & 1 & 1 \\\\
                   0 & 0 & 0 & 1 & 1 \\\\
                   2 & 2 & 2 & 3 & 3 \\\\
                   2 & 2 & 2 & 3 & 3 \end{pmatrix}\end{align}$$
                   
In other words, the larger matrix $M$ is defined such that $M$ at $(i, j)$ is the value of $m$ at $(f(i),f(j))$.

This construction turns out to be very useful when you want to define a matrix based on the attributes of a type. An example: for the model of notated music in my undergrad thesis, I construct a matrix of edge weights where indices are pitch classes (0 = C, 1 = C#, 2 = D etc.), from which I can define a matrix where the indices are _notes_ in a score: each note has a pitch class, so the 'map' (fulfilling the role of $f$ above) takes a note to its pitch class. 

## numpy approach!

Here is our `pullback` function, with the 'map' component implemented as a 1D numpy array.

{%gist 71fb43e446f1eaa45165fe1adf1261e7 %}

_Practically a one-liner!_ The `None` default value allows us to pass in no input matrix, in which case we use an identity matrix of the appropriate size. 

$f$ would be defined as a numpy array as follows (assuming `import numpy as np` has been called):

```
f = np.array([0,0,0,1,1])
```

$m$ is defined in the usual way, as a 2D numpy array:

```
m = np.array([[0,1],[2,3]])
```

As desired, here is our pullback output (as computed by the Python REPL):

```Python
>>> pullback(f, m)
array([[0, 0, 0, 1, 1],
       [0, 0, 0, 1, 1],
       [0, 0, 0, 1, 1],
       [2, 2, 2, 3, 3],
       [2, 2, 2, 3, 3]])
```
