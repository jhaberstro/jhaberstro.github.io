---
layout: post
title: Learning vs. Inference
---
In machine learning, the terms "learning" and "inference" are used often and it's not always clear what is meant. For example, is "variational inference" and neural network "inferencing" meant the same? Likely not.


When the deep learning crowd says "inference", what they mean is "perform a prediction." When the bayesian crowd says "inference", what they really mean is "compute the posterior distribution of a latent variable model"; e.g., for a model $$p(x, z) = p(x \mid z)p(z)$$ and data $$x$$, compute $$p(z \mid x)$$.

When someone says learning, they mean their model has a set of parameters and they need to find a good setting of those parameters; e.g. their model is actually $$p(x, z) = p(x \mid z;\theta)p(z)$$ and they want to find $$\theta$$. For frequentists, this means finding a point estimate of the parameters. For a bayesian, learning is inference because the parameters are treated as latent, random variables, so they can use normal variational inference or MCMC to compute a distribution over the parameters. Note that for 99% of deep learning practitioners, training is synonymous with learning as the practitioner will likely only be computing a single set of values (aka point estimate) for their weights.
