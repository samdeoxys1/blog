---
layout: single
title: "From Langevin to Fokker–Planck"
date: 2026-09-28
categories: mathematical_think_throughs
permalink: /from-langevin-to-fokker-planck/
---

Start with Langevin dynamics, which is a stochastic differential equation (SDE), i.e. describing the change of some state in time, but with some "noise"/variability in the process.

$$
dx=f(x,t)dt+g(x,t)dw,
$$

where $$x$$ is some state variable, $$f(x,t)$$ is the drift term that governs the deterministic part of the dynamics, and $$g(x,t)$$ is the diffusion term that governs the random part. $$dw$$ is the infinitesimal gaussian noise, with $$dw\sim Norm(0,dt)$$. It describe a "unit" stochastic move within $$dt$$. 
One interesting note is why its variance is $$dt$$? Because for independent noise, its variance add in time. Thus for $$dt$$ time, the variance accumulated by this "unit" move is $$dt$$. This is a bigger scale than the deterministic move, as the standard deviation or "typical" move is on the order of $$\sqrt{dt}$$, as opposed to $$dt$$ for deterministic moves. What would happen if we make the stochastic move the same scale as the deterministic move, i.e. variance $$dt^2$$ ? Then the accumulated change would have an extra $$dt$$ factor, and thus too small.

The SDE describes the evolution of one particle. How about the probability distribution of many particles following such dynamics? This description is described by $$p(x,t)$$. And we want to know how that distribution changes with time: $$\partial p(x,t)/\partial t=?$$

To solve this, use the definition:

$$
\partial p(x,t)/\partial t=\lim_{\Delta\rightarrow 0} \frac{p(x,t+\Delta t)-p(x,t)}{\Delta t},
$$

where the next time step probability can be given by:

$$
p(x,t+\Delta t):=p_{t+\Delta t}(x)=\int p_{\Delta t}(x\vert y)p_t(y)dy.
$$

The transition probability $$p_{\Delta t}(x\vert y)$$ here is given by the SDE and is approximately Gaussian (think of the SDE as a discrete markov chain, the mean of next step is current state plus the deterministic drift, and the variance is the diffusion coefficient $$g$$ squared times $$\Delta t$$)[^ito-convention]:

$$
l:=p_{\Delta t}(x\vert y)\propto e^{\frac{-(x-y-f(y,t)\Delta t)^2}{2g(y,t)^2\Delta t}}
$$


I still don't quite understand how people came up with this, but apparently when you don't know where to proceed, Taylor expansion is the way. From posthoc analysis, the likelihood term is gaussian, and if we Taylor expand the $$p_t(y)$$, then we could potentially integrate some stuff out, together with the gaussian term, into some moments. Concretely, Taylor expand near $$x$$[^taylor-expansion]:

$$
\begin{aligned}
p_t(y)&\approx p_t(x)+p_t'(x) (y-x)+\frac{1}{2}p_t''(x) (y-x)^2\\
p_{t+\Delta t}(x)&=\int l \cdot (p_t(x)+p_t'(x) (y-x)+\frac{1}{2}p_t''(x) (y-x)^2)dy\\
\end{aligned}
$$

We might expect these integrands to integrate nicely. However, we run into some awkwardness. For instance, the $$l$$ as a function of $$y$$ no longer integrates to $$1$$, because $$f$$ and $$g$$ depend on $$y$$. 
So instead, we expand around the displacement $$s=x-y$$. To do so we need help from delta function. Recall any function can be written as $$h(x)=\int h(y)\delta(x-y)dy$$, i.e. delta function select the one piece from the integral. 
The next time step probability is then given by:

$$
p_{t+\Delta t}(x)=\int\int p_{\Delta t}(y+s\vert y)p_t(y)\delta(x-y-s) dsdy.
$$

We can Taylor expand the delta function around the displacement $$s=0$$ (in the distributional sense, i.e. the effect is considered when integrated with smooth function, not evaluated at each point):

$$
\delta(x-y-s)\approx\delta(x-y)-\delta'(x-y)s+\frac{1}{2}\delta''s^2
$$

Recall $$\int h(y)\delta'(x-y)dy=h'(x)$$ and etc.
Plug this back in into the double integral, the first term becomes:

$$
\begin{aligned}
\int(\int p_{\Delta t}(y+s\vert y)p_t(y)\delta(y-x)ds)dy
&=\int(\int p_{\Delta t}(y+s\vert y)ds)p_t(y)\delta(y-x)dy\\
&=\int p_t(y)\delta(y-x)dy\\
&=p_t(x)
\end{aligned}
$$

This term will be used to cancel the $$p_t(x)$$ in the numerator of the difference quotient.
The second term becomes:

$$
\begin{aligned}
\int\int p_{\Delta t}(y+s\vert y)p_t(y)\delta'(x-y)sdsdy
&=\int(f(y,t)\Delta tp_t(y))\delta'(x-y)dy\\
&=\frac{\partial(f(x,t)p(x))}{\partial x}\Delta t
\end{aligned}
$$

This is because, the transition probability is gaussian in the displacement, with mean $$f\Delta t$$. And the integral with respect to $$s$$ is exactly its expectation.
By a similar derivation, the third term becomes:

$$
\frac{\partial^2(g(x,t)^2p_t(x))}{\partial x^2}\Delta t+O(\Delta t^2)
$$

Now it's the second moment of $$s$$ from the inner integral! Fortunately the extra term is $$O(\Delta t^2)$$ and thus too small and can be dropped, only keeping the variance term.
Go back to the difference quotient, we got the Fokker-Planck equation, hurray!

$$
\frac{\partial}{\partial t}p(x,t)=-\frac{\partial(f(x,t)p(x))}{\partial x}+\frac{1}{2}\frac{\partial^2(g(x,t)^2p_t(x))}{\partial x^2}
$$


[^ito-convention]: We follow the Itô convention, which discretize and evaluate functions like $$f$$ and $$g$$ at the previous time. Other convention exists, like Stratonovich, which evaluate at the midpoints, and can lead to a different form of the Fokker-Planck equation.

[^taylor-expansion]: Why is taylor expansion justified here? Because the likelihood is concentrated around inputs where $$x_t$$ and $$x_{t+\Delta t}$$ are close, because in infinitesimal time the movement is expected not to be too big.
