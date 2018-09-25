---
layout: post
title:  "Simple Polynomial Breeding in Ruby"
date:   2018-08-21 12:00:00 -0400
---

![Approximating f(x)=x^2 by Polynomial Breeding](/assets/polynomial.gif)

In the interest of learning more about inductive programming, I wanted to see how hard it would be to induce a polynomial function from no information other than a set of inputs and outputs. I am trying to imagine how, as an end-user uncomfortable with programming or mathematical abstraction, one might communicate an intended mathematical function or algorithm to a computer, and how effectively a computer might accommodate operating on effectively indeterminate information and communicate what it is doing back to the user in a digestible and intuitive manner. I've chosen to work with polynomial approximation because visualizing the computer's computational process for the end-user is logistically simple, consisting of generating animations out of graphs of the polynomials. And I've started with an evolutionary algorithm, a design pattern meant to emulate basic aspects of Charles Darwin's Theory of Evolution, simply because it sounds cute and cool, and it is conceptually simpler for less science-oriented people to understand.

Essentially, each polynomial in one variable may be treated as a proverbial chromosome whose encoded data is an array of real numbers representing the polynomial's coefficients ordered from lowest degree (constant) to highest. With a large enough population of pseudo-randomly generated chromosomes, we may apply a "fitness" or "cost" function to each chromosome in the population whereby cost is minimized when the chromosome best approximates the desired outputs given the example inputs. Then we may program in a "generational time-step function" where we drop the worst scorers and "breed" the top scorers on each iteration. Over enough generational steps, we should arrive at a local best-fit curve for the inputs and outputs. To best ensure that a global best-fit curve is found, it will also be necessary to introduce mutations into some chromosomes each evolutionary step. We can then graph each chromosome at each time step and build a movie for users to watch. Other forms of visual interaction, such as graphing the current state in real time rather than building movies starting from the beginning on-demand, isn't too much harder to develop.

To run 