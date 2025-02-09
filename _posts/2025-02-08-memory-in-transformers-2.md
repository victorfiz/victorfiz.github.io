---
layout: default
title: "Memory in Transformers (2): Associative Memory as Test-Time Regression"
subtitle: Instead of naively compressing the KV cache, new architectures (e.g. Mamba, RWKV, DeltaNet, Titans, etc.) differentially weigh associative memories. We can generalise these approaches as test-time regression solvers that dynamically adjust weights to reflect relevance of past information.
date: 2025-02-08
categories: blog
type: technical
---

To recap, we showed that a KV cache can be compressed into a finite hidden state that cleanly retrieves a value, given the associated key. We can phrase this as a minimisation problem,  

$$
\min_{m \in \mathcal{M}} \frac{1}{2} \sum_{i=1}^t \gamma_i \,\| \mathbf{v}_i - m(k_i)\|_2^2
$$

where a model, $\mathcal{M}$ learns to minimise the retrieval of a value, given a key, weighted by how important this association should be, $\gamma_i$. Here, we will see how this genralisation leads to different architectures for associative memory.

### Associative Memory as Test-Time Regression


Today, everyone and their dog is releasing a {% include tooltip.html term="new linear Transformer architecture" definition="see Mamba-2, RWKV, DeltaNet, Titans, Hyena, Longhorn, Based, mLSTM, etc" %}. They mostly belong to a few {% include tooltip.html term="families" definition='see Linear Attention, Gated Linear RNNs, State-space models, etc' %} that take different approaches at the memory compression problem. [Wang et al., (2025)](https://arxiv.org/pdf/2501.12352) neatly summarise how this is just test-time regression, like above, with each family adopting different regressor functions, weightings, and optimisation algorithms. 

Let's take our vanilla linear attention and show that this is just an approximation of **linear least squares** i.e. {% include tooltip.html term="no" definition="i.e. each error contributes equally to the loss. Consider that more recent information should be more strongly memorised, so we could place a greater emphasis on the error, by varying $\gamma_i$ over time. LLS does not use this inductive bias." %} $\gamma_i$, $\mathcal{M}$ restricted to being linear, and solvable analytically:

$$
m_{\text{linear}} = \arg\min_{m \in \mathcal{M}} \frac{1}{2} \sum_{i=1}^t \,\| \mathbf{v}_i - m(k_i)\|_2^2 = \mathbf{V}^\top \mathbf{K} \left( \mathbf{K}^\top \mathbf{K} \right)^{-1} \scriptstyle{\mid} \, {\scriptstyle t \, > \, d_{\text{keys}}}
$$

Calculating $\small{\left( \mathbf{K}^\top \mathbf{K} \right)^{-1}}$ is computationally expensive, but if we assume that {% include tooltip.html term="key embeddings are approximately orthogonal" definition="This can only be true of course if ${\scriptstyle  d_{\text{keys}} \, \geq  \,  t}$, since then each embedding can inhabit a unique orthogonal dimension. Essentially we choose to ignore the covariance between the dimensions of the key vectors. The only way to prevent this is by increasing the dimensions of the square hiddent state." %} to one another, we can make the approximation $\mathbf{K}^\top \mathbf{K} = \mathbf{I}$. Notice now that we have {% include tooltip.html term="solved" definition="We have used a second-order solver since it involves a matrix inversion, which corresponds to solving a quadratic system." %} $M_{\text{t}} \approx \sum_{i=1}^t \mathbf{v_i}\mathbf{k_i}^T$, which is just our compressed KV-cache. Here $\phi (\mathbf{k_i})$ is a linear kernel, but we can also use linear attention with a nonlinear feature map, which can be solved by the same approximation as above. This is essentially what [The Hedgehog](https://openreview.net/pdf?id=4g02l2N2Nx) and [DiJian](https://openreview.net/pdf?id=0uUHfhXdnH) do.

Unlike linear attention, gated linear attention and state-space models implement time-decayed memory updates. We can model this as **weighted linear least squares**,

$$
m_{\text{weighted linear}} = \arg\min_{m \in \mathcal{M}} \frac{1}{2} \sum_{i=1}^t \gamma^{(t)} \,\| \mathbf{v}_i - m(k_i)\|_2^2 = \mathbf{V}^\top \boldsymbol{\Gamma} \mathbf{K} \left( \mathbf{K}^\top \boldsymbol{\Gamma} \mathbf{K} \right)^{-1} \scriptstyle{\mid} \, {\scriptstyle t \, > \, d_{\text{keys}}}
$$

where we geometrically decay in time, $\gamma_i^{(t)} = \prod_{j=i+1}^{t} \gamma_j$, and $\boldsymbol{\Gamma}$ is a diagonal of $\gamma_i^{(t)}$ in time. Following the orthogonality approximation before, $\mathbf{K}^\top \boldsymbol{\Gamma} \mathbf{K} \approx \mathbf{I}$, we find an approximate analytical solution, which we can state as a recurrence relationship,

$$
\mathbf{M}_t \approx \sum_{i=1}^t \gamma_i^{(t)} \mathbf{v}_i \mathbf{k}_i^\top 
= \sum_{i=1}^t \left( \prod_{j=i+1}^t \gamma_j \right) \mathbf{v}_i \mathbf{k}_i^\top 
= \gamma_t \sum_{i=1}^{t-1} \gamma_i^{(t-1)} \mathbf{v}_i \mathbf{k}_i^\top + \mathbf{v}_t \mathbf{k}_t^\top 
= \gamma_t \mathbf{M}_{t-1} + \mathbf{v}_t \mathbf{k}_t^\top
$$

This looks a lot like recent state-space models ([Mamba-2](https://arxiv.org/pdf/2405.21060)) and gated linear attention ([GLA](https://proceedings.mlr.press/v235/yang24ab.html), [RWKV-6](https://arxiv.org/pdf/2404.05892), [mLSTM](https://arxiv.org/pdf/2405.04517)).

The methods above solve the regression problem analytically, via a second-order solution. In order to be computationally feasible, they approximate the solution by ignoring the inversion, which leads to poor results in practice. We can instead try a first order solution like **online gradient descent**, which is computationally less expensive. This looks a lot more like what Schmidhuber had in mind with online learning via fast weights. Here, our model is not longer a naively summed hidden state – it's an MLP that learns. For SGD, this looks like,

$$
\nabla \mathcal{L}_t(\mathbf{M}) = \nabla \sum_{i=1}^t \frac{1}{2} \|\mathbf{v}_i - \mathbf{M}\mathbf{k}_i\|_2^2 = (\mathbf{M}\mathbf{K}^\top - \mathbf{V}^\top)\mathbf{K}
$$

We can now write the recurrence relationship as,

$$
\begin{align*}
\mathbf{M}_t &= \mathbf{M}_{t-1} - \beta_t \nabla \mathcal{L}_t(\mathbf{M}_{t-1}) \\
             &= \mathbf{M}_{t-1} + \beta_t (\mathbf{v}_t - \mathbf{M}_{t-1} \mathbf{k}_t) \mathbf{k}_t^\top \\
             &= \mathbf{M}_{t-1} (\mathbf{I} - \beta_t \mathbf{k}_t \mathbf{k}_t^\top) + \beta_t \mathbf{v}_t \mathbf{k}_t^\top
\end{align*}
$$

where $\beta_t$ is the learning rate or step-size for batched updates. The "learning to memorise at test-time" paradigm is exaclty this. It's used by [TTT](https://arxiv.org/pdf/2407.04620) and [DeltaNet](https://arxiv.org/pdf/2406.06484). [Titans](https://arxiv.org/pdf/2501.00663) also uses this, but adds momentum by having two hidden states, which they show stabilises learning. [Longhorn](https://arxiv.org/pdf/2407.14207) uses adaptive updates 



You can now start to appreciate the immense design freedom we can have with this, beyond vanilla SGD. Some ideas [Wang et al., (2025)](https://arxiv.org/pdf/2501.12352) proposed include adaptive

Again, we see that we just generate an online sequence of associative memories that can map our keys back to our values. The diagram below shows how the online-learning method differs from recursive least squares,

<div style="text-align: center; margin: 20px 0;">
    <img src="/assets/images/posts/2025-02-02/update-rule-diagram.png" alt="Second order vs First order Solvers" style="width: 90%; max-width: 800px; filter: brightness(1.2);">
</div>





<!-- 
This is highly unparallelisable, but we can use a trick called the {% include tooltip.html term="chunkwise parallel form" definition="The chunkwise parallel form splits sequences into $\frac{L}{C}$ chunks, reducing memory and computational cost by processing in smaller parallel blocks ($C$) while propagating information between chunks. It interpolates between full parallel ($C = L$) and recurrent ($C = 1$) forms, achieving $O(LCd + Ld^2)$ complexity and enabling efficient handling of long sequences." %}.


Some thoughts on the future of this... -->


