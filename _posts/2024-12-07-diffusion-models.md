---
layout: default
title: Diffusion Models
subtitle: An exploration of the mathematical foundations of diffusion models, covering forward and reverse processes, ELBO, and training objectives.
date: 2024-12-07
categories: blog
type: technical
---

Diffusion models approximate a data distribution $q(x)$ by gradually adding noise to the data through a forward process and learning to reverse this corruption via a generative reverse process. The goal is to produce samples $x^{(0)} \sim p_\theta(x^{(0)})$ that resemble data $x \sim q(x)$.

The forward process progressively transforms data into noise by defining a Markov chain:

$$
q(x^{(1:T)} | x^{(0)}) = \prod_{t=1}^T q(x^{(t)} | x^{(t-1)})
$$

where each step adds Gaussian noise:

$$
q(x^{(t)} | x^{(t-1)}) = \mathcal{N}(x^{(t)}; \sqrt{\alpha_t} x^{(t-1)}, \beta_t I)
$$

Here, $\alpha_t = 1 - \beta_t$ controls the noise scale. Over $T$ steps, the data distribution $q(x^{(0)})$ transitions to an approximate Gaussian: $q(x^{(T)}) \approx \mathcal{N}(0, I)$.

The reverse process aims to denoise step-by-step, reconstructing data from noise:

$$
p_\theta(x^{(0:T)}) = p(x^{(T)}) \prod_{t=1}^T p_\theta(x^{(t-1)} | x^{(t)})
$$

The reverse conditional $p_\theta(x^{(t-1)} \mid x^{(t)})$ is parameterised as:

$$
p_\theta(x^{(t-1)} \mid x^{(t)}) = \mathcal{N}(x^{(t-1)}; \mu_\theta(x^{(t)}, t), \Sigma_\theta(x^{(t)}, t))
$$

In practice, $\Sigma_\theta$ is often fixed to simplify training, leaving $\mu_\theta$ as the primary learnable component.

To train the model, we maximise a variational lower bound (ELBO) on the data log-likelihood. Using the forward process as the variational posterior, the ELBO is:

$$
\log p_\theta(x^{(0)}) \geq \mathbb{E}_{q(x^{(1:T)} | x^{(0)})}\left[\log \frac{p_\theta(x^{(0:T)})}{q(x^{(1:T)} | x^{(0)})}\right]
$$

Substituting the Markov chain formulations of $q$ and $p_\theta$, the ELBO expands to:

$$
\mathbb{E}_{q}\left[-\sum_{t=1}^T \text{KL}\big(q(x^{(t-1)} | x^{(t)}, x^{(0)}) \| p_\theta(x^{(t-1)} | x^{(t)})\big) + \log p_\theta(x^{(0)} | x^{(1)}) \right]
$$

The KL divergence terms ensure the learned reverse process closely approximates the true dynamics of the forward process. 

The forward process allows direct sampling of any step $t$ given the clean data $x^{(0)}$. This property avoids the need to sample every intermediate step:

$$
q(x^{(t)} | x^{(0)}) = \mathcal{N}\big(x^{(t)}; \sqrt{\overline{\alpha}_t} x^{(0)}, (1 - \overline{\alpha}_t) I\big)
$$

where $\overline{\alpha}_t = \prod_{s=1}^t \alpha_s$. Rewriting this, $x^{(t)}$ is expressed as:

$$
x^{(t)} = \sqrt{\overline{\alpha}_t} x^{(0)} + \sqrt{1 - \overline{\alpha}_t} \epsilon, \quad \epsilon \sim \mathcal{N}(0, I)
$$

This reparameterisation simplifies the reverse process, as $\mu_\theta$ can be redefined in terms of the original data $x^{(0)}$ and added noise $\epsilon$.

For training, the reverse process is reformulated as predicting the noise $\epsilon$ used in the forward process. The simplified objective becomes:

$$
L(\theta) = \mathbb{E}_{x^{(0)}, \epsilon, t}\left[\| \epsilon - \epsilon_\theta(x^{(t)}, t) \|^2\right]
$$

where $\epsilon_\theta$ is a neural network prediction of the noise. Intuitively, this task requires the network to denoise the input $x^{(t)}$.

At test time, sampling involves starting from $x^{(T)} \sim \mathcal{N}(0, I)$ and iteratively applying the learned reverse process:

$$
x^{(t-1)} \sim p_\theta(x^{(t-1)} | x^{(t)})
$$

This produces a final sample $x^{(0)}$ that approximates the data distribution $q(x)$.

Diffusion models are robust due to their reliance on Gaussian transitions and the reparameterised noise prediction objective.

