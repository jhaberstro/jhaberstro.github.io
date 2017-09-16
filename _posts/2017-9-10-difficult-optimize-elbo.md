---
layout: post
title: Why is the ELBO difficult to optimize?
---

The task of bayesian inference is to compute the posterior $$p(z \mid x)$$ of the model $$p(x, z) = p(x \mid z)p(z)$$ where $$z$$ is a latent variable. This is often intractable, so a bayesian may resort to approximating it with some easier distribution $$q(z)$$ — this method is called variational inference . Usually the approximating distribution is chosen from a parameterized family of distributions. Thus $$q(z)$$ is really the variational distribution $$q(z; \lambda)$$ for some chosen value \lambda. How is this parameter \lambda chosen?

A common method is to minimize the KL-divergence between the variational distribution and the true posterior, $$KL[q(z; \lambda) \| p(z \mid x)]$$. Of course, the posterior isn’t actually known, but if we break down this KL divergence, we see the following:

$$KL[q(z) \| p(z \mid x)] = \mathbb{E}_{q(z)}[log(q(z)) - log(p(z \mid x))]$$

$$= \mathbb{E}_{q(z)}[log(q(z)) - log(\frac{p(x, z)}{p(x)})]$$

$$= \mathbb{E}_{q(z)}[log(q(z)) - log(p(x, z)) + log(p(x))]$$

$$= \mathbb{E}_{q(z)}[log(q(z)) - log(p(x, z))] + log(p(x))$$

$$= \mathbb{E}_{q(z)}[log(q(z)) - log(p(x \mid z)) - log(p(z))] + log(p(x))$$

The KL divergence can be minimized up to a constant (the marginal likelihood) wrt the variational parameters since we know how to compute $$p(x, z)$$ per the model’s definition. This term that we want to minimize — $$E_q(z)[log(q(z)) - log(p(x \mid z)) - log(p(z))]$$ — is the negative evidence lower bound (ELBO). So, back to the original title question — why is the ELBO difficult (but not impossible) to optimize?

It actually isn’t difficult to optimize for certain classes of models. If all the latent variables are chosen to be independent in the variational distribution (i.e. the mean-field assumption) and the model is conditionally conjugate, then one can use coordinate ascent variational inference.  This method is fast to compute. Unfortunately, the restrictions it places on the model are also quite limiting. It is when the model is unconstrained is optimizing the ELBO difficult. But again, why?

If nothing is known about the model $$p(x \mid y)p(y)$$ except a method to evaluate it, we are relegated to optimizing the ELBO by gradient descent. This entails computing the gradient of the ELBO (excuse my abuse of notation):

$$\nabla_\lambda \mathbb{E}_{q(z)}[log(q(z)) - log(p(x \mid z)) - log(p(z))]
= \nabla_\lambda \int q(z)(log(q(z)) - log(p(x \mid z)) - log(p(z))) dz$$

$$= \nabla_\lambda (\int q(z)log(q(z)) dz - \int q(z)(log(p(x \mid z)) + log(p(z)) dz))$$

$$= (\int \nabla_\lambda q(z) log(q(z)) dz) - (\int (\nabla_\lambda q(z))(log(p(x \mid z)) + log(p(z)) dz))$$

where the I've shortened $$q(z; \lambda)$$ to $$q(z)$$.

When I first saw this equation, I wasn’t sure what the big deal was. This is because I wasn’t accustom to staring at math equations all day, but to any (applied) mathematician it should be obvious. I’ll put in bold font: computing the gradient of the ELBO *requires computing some likely high-dimensional, likely intractable integral.* Why is it intractable? Well, you may get lucky and the integral has an analytic solution, but in general that won’t be true. Also, because  this quantity no longer takes the form of an expectation, it can’t easily be estimated by Monte Carlo. It may be possible to do so if z is discrete and its sample space is small (as you could exhaustively evaluate the integrand at all z), but that implies z is very low dimensional which may not be true either.

Luckily there exist some methods to side-step these issues by placing mild(ish)  constraints on either the variational family of distributions or both the variational family and the model. These methods are the score function gradient and reparameterization gradient, but I won’t be discussing them in this post — they’re best left to be explained another day (or by Google)..
