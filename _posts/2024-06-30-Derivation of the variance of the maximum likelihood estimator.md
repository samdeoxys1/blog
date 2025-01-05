---
layout: single
title: "Derivation of the variance of the maximum likelihood estimator"
date: 2024-06-30
categories: mathematical details
---

# Intro

While reading papers about Fisher information, I came across this basic relationship between the Fisher information and the variance of the maximum likelihood estimator (for large sample size), which I found amazing:

$$var[\hat\theta]=\mathbb E[(\hat\theta-\theta)^2]=1/(NI(\theta)),$$

where $$\theta$$ is the true parameter in the generative model, and the $$\hat \theta$$ is the maximum likelihood estimator, the Fisher information $$I(w)=\mathbb E[-\frac{\partial^2 \log p(x\vert w)}{\partial w^2}]$$, and $$N$$ is the sample size. (using $$w$$ here for a generic input, to not be confused with the true parameter)

This relationship is really interesting because the fisher information involves the (derivatives of the) log likelihood function and not the estimator itself, and thus a priori from the definition it is not intuitive to see this relationship.  

The following snippet will simply provide a derivation as I would do it, then the difficulty I encountered and the “correct” way as I understood from the textbook.  

# Derivation (with caveats)

Assuming the samples $$\{x^i\}_i\sim p(x\vert \theta)$$ are i.i.d. 

We need to somehow relate $$(\hat \theta-\theta)^2$$ to the derivatives of $$\log p(x\vert w=\theta)$$ with respect to $$w$$. The way to do so is Taylor expansion! To get the second derivative, we Taylor expand the (empirical estimate of the) first derivative of the log likelihood function, which we call $$U(w)=\frac{1}{N}\sum_{i=1}^{N}\frac{\partial \log p(x^i\vert w)}{\partial w},$$ at the true parameter $$\theta$$:

$$
U(\hat\theta)=U(\theta)+(\hat \theta-\theta)U'(\theta)
$$

Because $$\hat \theta$$ maximizes the log likelihood, $$U(\hat \theta)=0$$, we get (assuming the second derivative of the log likelihood is not zero at the true parameter)

$$
(\hat \theta-\theta) = -\frac{U(\theta)}{U'(\theta)}
$$

Note that as $$N\rightarrow \infty$$, $$U'(\theta)=\frac{1}{N}\sum_{i=1}^{N}\frac{\partial^2 \log p(x^i\vert w)}{\partial w^2}\rightarrow-I(\theta),$$  by the strong law of large numbers. Thus we replace (!) $$U'(\theta)$$ in the above, square both sides and take the expectation:

$$
\mathbb E[(\hat\theta-\theta)^2] = \frac{\mathbb E[U(\theta)^2]}{I^2(\theta)}.
$$

Expand the numerator:

$$\mathbb E[U(\theta)^2]=1/N^2(\sum_i\mathbb E(\frac{\partial \log p(x^i\vert w=\theta)}{\partial w})^2+\sum_{i,j}\mathbb E[\frac{\partial \log p(x^i\vert w=\theta)}{\partial w}] \mathbb E[\frac{\partial \log p(x^j\vert w=\theta)}{\partial w}]).$$

The cross terms are 0, since the expectation of the derivative of the log likelihood is 0 under the true parameter. The square terms:  
$$\mathbb E[(\frac{\partial \log p(x^i\vert w=\theta)}{\partial w})^2] = I(\theta)$$ (this is a common form of fisher information other than the second derivative form. The equivalence of two forms is straightforward to show using integration by part and is omitted here). 

Together, we have:

$$
\mathbb E[(\hat \theta-\theta)^2]=\frac{1}{N I(\theta)}
$$

# Confusion and correction

❗One thing that bugged me was when we replace the $$U'(\theta)$$ with its asymptotic value under large sample size. In calculations involving random variables, when can we replace variables with what they converge to? For instance, why can’t we replace $$U(\theta)$$ with $$\mathbb E[\partial \log p(x\vert w=\theta)/\partial w],$$ which would be 0? 

This confusion comes from treating the problem in a heuristic way, and reason about the variance of the estimator in the large sample case, rather than reason about the distribution of the estimator itself. More rigorously, the claim is actually: $$\sqrt{N}(\hat \theta-\theta) \rightarrow N(0,1/I(\theta))$$, where the convergence is in distribution (called ***asymptotic normality***). (convergence in distribution: probability statements about a random variable can be approximated using the asymptotic distribution. It’s the probability statements that are being approximated, not the random variable itself!)

Let’s revisit $$\hat \theta - \theta=-\frac{U(\theta)}{U'(\theta)}$$ and examine what distribution it converges to. As in the set up of central limit theorem, it’s more useful to consider $$\sqrt{N} (\hat \theta-\theta)$$ (perhaps I should think through this in a post about central limit theorem someday. My current understanding is: without this scaling, the law of large number would just say things converge to a point. With the $$\sqrt{N}$, some fluctuation is maintained). $$\sqrt{N}U(\theta)$$ converges in distribution to $$N(0,I(\theta))$, using the second moment of $$U(\theta)$$ computed in the previous section. $$U'(\theta)$$ converges almost surely to $$I(\theta)$$ by the strong law of large numebrs, which implies convergence in distribution. Then by Slutzky’s theorem (if $$X_n\rightarrow X$$ and $$Y_n\rightarrow c$$, then $$X_nY_n\rightarrow cX$$, arrow here means convergence in distribution; c is constant and all else are random variables) and the theorem that convergence in distribution is preserved after passing through a function (i.e. here $$U'(\theta)$$ is transformed into $$1/U'(\theta)$$), we get $$\sqrt{N} U(\theta)/U'(\theta)\rightarrow \mathcal N(0,I(\theta)) /I(\theta) =\mathcal N(0,1/I(\theta))$$.

Now let me repeat my question from earlier, i.e. can we replace $$U(\theta)$$ by some constant (i.e. 0) it converges to as well? The answer is: it would not be helpful, as we still have a $$\sqrt{N}$$ term that is growing with the sample size. It is more helpful to absorb the $$\sqrt{N}$$ term in $$U(\theta)$$ to simplify the expression with the central limit theorem. 

The crucial thing I learned in this detour is the difference in scaling between the law of large numbers ($$1/N$$) and central limit theorem ($$1/\sqrt{N}$$). In the limit of large $$N$$, the $$1/N$$ makes things converge to a point, whereas the $$\sqrt{N}$$ preserves a non-zero variance.

# Reference

1. Wasserman, L. (2013). *All of statistics: a concise course in statistical inference*. Springer Science & Business Media.
