---
layout: single
title: "How not to learn hyperparameters"
date: 2025-06-28
categories: mathematical_think_throughs
---

The most prevelant way of learning the parameters of a probabilistic model is to do maximum likelihood estimation:

$$
\begin{equation}
\hat w=\text{argmax }p(y|w).
\end{equation}
$$

If in addition, we put a prior on $w$ as regularization, the estimator becomes the *maximum a posteriori* (MAP) estimator:

$$
\begin{align}
\hat w&=\text{argmax } L(w,\sigma) \nonumber \\
L(w,\sigma)&=p(y,w|\sigma)=p(y|w)+p(w|\sigma),
\end{align}
$$

where $\sigma$ is the hyperparameter. For instance, in linear regression, we can put a gaussian prior $w\sim Norm(0,\sigma^2)$ to encourage a small 2-norm. Here, we assume $\sigma^2$ is known. 

But I don't like arbitrary quantities in a model. Can we learn this?

Yes, the common ways are: 1) **Cross validation** and 2) **Empirical Bayes** (or maximum marginal likelihood or Type-II maximum likelihood). 

But both seem more complicated than an obvious alternative. In the spirit of maximum likelihood, <u>why can't we treat $p(y,w|\sigma)$ as a likelihood function of $\sigma$ and optimize it? In the following I will explain why this is a bad idea.</u>



# Why "maximum likelihood" is a bad idea for hyperparameter learning  

Let's consider the example of a 1-D linear regression where:
$$
\begin{align}
y= wx+\epsilon\\
w\sim Norm(0,\sigma^2) & \text{: parameter}\\
\epsilon\sim Norm(0,\delta^2). & \text{: noise, assume to be known}
\end{align}
$$

### The effortful explanation (skip if you don't like erivations)

The log joint is given by (ignoring the $\pi$ terms):
$$
\begin{equation}
L(w,\sigma)=\log p(y,w|x,\sigma,\delta)\sim -\frac{\sum(y_i-wx_i)^2}{2\delta^2}-\log|\delta| - \frac{w^2}{2\delta^2}-\log |\sigma|
\end{equation}
$$

Perform maximum likelihood with respect to $\sigma$, we get the gradient:

$$
\begin{equation}
\frac{\partial}{\partial \sigma^2}L(w,\sigma)=\frac{w^2}{2\sigma^4}-\frac{1}{2\sigma^2}. \label{dsigma}
\end{equation}
$$

Set this to $0$, we get a rather simple formula: 

$$
\begin{equation}
\hat \sigma^2=w^2 \label{sigma_hat}
\end{equation}
$$

This is saying at the stationary point of $L$, we have the variance equal the square of the parameter. This might give a tempting way of choosing the prior variance, **however, we have not verified that the stationary point is a maximum**! In fact, if we check by taking the derivative of eq. $\ref{dsigma}$, we get:

$$
\begin{aligned}
\frac{\partial^2}{(\partial \sigma^2)^2}L &=-\frac{w^2}{(\sigma^2)^3}+\frac{1}{2(\sigma^2)^2}\\
&=\frac{1}{2(\sigma^2)^2}>0. &(\text{plug in eq. \ref{sigma_hat}})
\end{aligned}
$$

So the second derivative of the objective with respect to the prior variance $\sigma^2$ is positive at the stationary point (think of a picture of curving up like a smile). Having a single positive diagonal entry rules out the case where all eigenvalues of the hessian are negative, and thus the stationary points cannot be maxima!    

### The intuition explanation

We can save the sweat by realizing -- the role of $\sigma^2$ is to regularize $w$ and preventing it from going too large. Imagine $w$ as a wandering dog and $\sigma^2$ the leash. Just as a longer leash means the dog can wander more freely, a larger $\sigma^2$ means a looser constraint. But there is nothing constraining $\sigma^2$! Therefore we can keep lengthening the leash to give the dog more freedom, and our objective will keep increasing this way! 



Next time we will discuss how Empirical Bayes does not have such a problem. 

