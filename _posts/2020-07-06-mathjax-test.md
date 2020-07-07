---
layout: post
title: Pullbacks in numpy
published: true
---
My undergraduate thesis made frequent use of a "pullback" construction for describing large matrices in terms of smallers ones, by performing an operation on the indices of the large matrix. 

I'll show what I mean by way of an example.

Let us define our smaller matrix as follows:

To perform a pullback, we need a map $f: {\mathcal Z}_8 \to {\mathcal Z}_4$.

Let us define our map as follows.
$$\begin{align}
f: 0 &mapsto 0 \\\\
f: 1 &mapsto 0 \\\\
f: 2 &mapsto 0 \\\\
f: 3 &mapsto 0 \\\\
f: 4 &mapsto 0 \\\\
f: 5 &mapsto 1 \\\\
f: 6 &mapsto 2 \\\\
f: 7 &mapsto 3 \\\\
f: 7 &mapsto 3 \\\\
\end{align}$$
