---
layout: post
title:  "Blogging with MathJax"
date:   2016-05-25 07:15:14 -0400
categories: data
---

How to write about math on the internet.

MathJax is an open source JavaScript display engine for mathematics that works in all browsers. MathJax enables you to write LaTeX-like equations that get nicely displayed in a web browser, like this: 

$$a^2 + b^2 = c^2$$

And this:

$$\frac{n!}{k!(n-k)!} = \binom{n}{k}$$

And this:

$$
X \rightarrow Y \rightarrow \hat{X}
$$

$$
\Rightarrow
$$

$$
I(\hat{X};X) \leq I(Y;X)
$$

And this:

$$
A_{m,n} = 
 \begin{pmatrix}
  a_{1,1} & a_{1,2} & \cdots & a_{1,n} \\
  a_{2,1} & a_{2,2} & \cdots & a_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  a_{m,1} & a_{m,2} & \cdots & a_{m,n} 
 \end{pmatrix}
$$

You can even make colorful equations like:

$$
\definecolor{energy}{RGB}{114,0,172}
\definecolor{freq}{RGB}{45,177,93}
\definecolor{spin}{RGB}{251,0,29}
\definecolor{signal}{RGB}{18,110,213}
\definecolor{circle}{RGB}{217,86,16}
\definecolor{average}{RGB}{203,23,206}
\color{energy} X_{\color{freq} k} \color{black} =
\color{average} \frac{1}{N} \sum_{n=0}^{N-1}
\color{signal}x_n \color{spin}
e^{\mathrm{i} \color{circle} 2\pi \color{freq}k
\color{average} \frac{n}{N}}
$$

The [LaTeX wikibook](https://en.wikibooks.org/wiki/LaTeX/Mathematics) is a useful resource for examples and syntax.

----------------

## Setting up MathJax with Jekyll

One of the benefits of blogging with [Jekyll](https://jekyllrb.com/) and [GitHub Pages](https://pages.github.com/) is how easy it is to write in MathJax.

To load the MathJax javascript on blog posts, add the following lines in the `post.html` file located in the `_layouts` folder.

```html
<script type="text/javascript"
    src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

That's all! Now you can start writing in MathJax.

## Some examples

Let's try an example. How to write the simple equation:

$$a^2 + b^2 = c^2$$

How do you write this? Very simple: use *double dollar signs*

```html
$$
a^2 + b^2 = c^2
$$
```

#### Fractions:

$$
\frac{n!}{k!(n-k)!} = \binom{n}{k}
$$

```html
$$
\frac{n!}{k!(n-k)!} = \binom{n}{k}
$$
```

#### Continued Fractions:

$$
\begin{equation}
  x = a_0 + \cfrac{1}{a_1 
          + \cfrac{1}{a_2 
          + \cfrac{1}{a_3 + \cfrac{1}{a_4} } } }
\end{equation}
$$

```html
\begin{equation}
  x = a_0 + \cfrac{1}{a_1 
          + \cfrac{1}{a_2 
          + \cfrac{1}{a_3 + \cfrac{1}{a_4} } } }
\end{equation}
```

#### Symbols:

$$
X \rightarrow Y \rightarrow \hat{X}
$$

$$
\Rightarrow
$$

$$
I(\hat{X};X) \leq I(Y;X)
$$

```html
$$
X \rightarrow Y \rightarrow \hat{X}
$$

$$
\Rightarrow
$$

$$
I(\hat{X};X) \leq I(Y;X)
$$
```

#### Matrices:

$$
A_{m,n} = 
 \begin{pmatrix}
  a_{1,1} & a_{1,2} & \cdots & a_{1,n} \\
  a_{2,1} & a_{2,2} & \cdots & a_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  a_{m,1} & a_{m,2} & \cdots & a_{m,n} 
 \end{pmatrix}
$$

```html
$$
A_{m,n} = 
 \begin{pmatrix}
  a_{1,1} & a_{1,2} & \cdots & a_{1,n} \\
  a_{2,1} & a_{2,2} & \cdots & a_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  a_{m,1} & a_{m,2} & \cdots & a_{m,n} 
 \end{pmatrix}
$$
```

#### Inserting space with `quad`:

$$
f(n) =
  \begin{cases}
    n/2       & \quad \text{if } n \text{ is even}\\
    -(n+1)/2  & \quad \text{if } n \text{ is odd}\\
  \end{cases}
$$

```html
f(n) =
  \begin{cases}
    n/2       & \quad \text{if } n \text{ is even}\\
    -(n+1)/2  & \quad \text{if } n \text{ is odd}\\
  \end{cases}
```

------------------


## Using color

In order to use color, you need to include the following script in the `post.html` file in the `_layouts` folder in your Jekyll file structure:

```html
  <script type="text/x-mathjax-config">
    MathJax.Hub.Config({ TeX: { extensions: ["color.js"] }});
  </script>
```

And now you can start writing MathJax in color! 

Define colors using the `\definecolor` command, providing a color name and RBG code. Implement colors using the `\color` command preceding the text that you want to color.

$$
\definecolor{energy}{RGB}{114,0,172}
\definecolor{freq}{RGB}{45,177,93}
\definecolor{spin}{RGB}{251,0,29}
\definecolor{signal}{RGB}{18,110,213}
\definecolor{circle}{RGB}{217,86,16}
\definecolor{average}{RGB}{203,23,206}

\color{energy} X_{\color{freq} k} \color{black} =
\color{average} \frac{1}{N} \sum_{n=0}^{N-1}
\color{signal}x_n \color{spin}
e^{\mathrm{i} \color{circle} 2\pi \color{freq}k
\color{average} \frac{n}{N}}

$$

```html
$$
\definecolor{energy}{RGB}{114,0,172}
\definecolor{freq}{RGB}{45,177,93}
\definecolor{spin}{RGB}{251,0,29}
\definecolor{signal}{RGB}{18,110,213}
\definecolor{circle}{RGB}{217,86,16}
\definecolor{average}{RGB}{203,23,206}

\color{energy} X_{\color{freq} k} \color{black} =
\color{average} \frac{1}{N} \sum_{n=0}^{N-1}
\color{signal}x_n \color{spin}
e^{\mathrm{i} \color{circle} 2\pi \color{freq}k
\color{average} \frac{n}{N}}
$$
```

## Useful resources

* [LaTeX wikibook](https://en.wikibooks.org/wiki/LaTeX/Mathematics) is a useful resource for examples and syntax.

* [RapidTables](http://www.rapidtables.com/web/color/blue-color.htm) for RGB color codes.

Thanks to [Matt Adereth](http://adereth.github.io/blog/2013/11/29/colorful-equations/) and [Gaston Sanchez](http://gastonsanchez.com/opinion/2014/02/16/Mathjax-with-jekyll/) for the helpful guidance!

