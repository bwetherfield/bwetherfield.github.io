---
layout: post
title: Pullbacks in numpy
published: true
---
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

$$\begin{align} M &= f^{-1} m \\\\

&= \begin{pmatrix} 0 & 0 & 0 & 1 & 1 \\\\ 
                   0 & 0 & 0 & 1 & 1 \\\\
                   0 & 0 & 0 & 1 & 1 \\\\
                   2 & 2 & 2 & 3 & 3 \\\\
                   2 & 2 & 2 & 3 & 3 \end{pmatrix}\end{align}$$