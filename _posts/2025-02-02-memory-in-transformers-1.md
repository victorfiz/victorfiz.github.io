---
layout: default
title: "Memory in Transformers (1): Linear Attention"
subtitle: Transformers use a KV-cache as their working memory. This scales linearly with context size, when it should scale with information. Linear attention seeks to compress this cache into a finite hidden-state – which can be understood as selective associative memory.
date: 2025-02-02
categories: blog
type: technical
---

<span style="color: #D3D3D3;">Feel free to skip this if you have come across linear attention before.</span>

The insight [{% include tooltip.html term="attention" definition="Attention genereally, but I will be speaking about attention in the causally-masked autoregressive sense here" %}](https://arxiv.org/pdf/1409.0473) brought was that tokens do not exist in a vacuum, they are {% include tooltip.html term="shaped by their context" definition="More specifically, each token is differentially affected by each part of the context to different extents. I imagine this was understood before [Bahdanau et al., 2015](https://arxiv.org/pdf/1409.0473) proposed attention" %}. [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762) proposed that the information a newly predicted token receives, $\mathbf{o_t}$, is just the weighted sum of information, $\mathbf{v_i}$, emitted by all prior tokens. This weighting is determined by an arbitrary score of similarity, $\text{sim}(\mathbf{q_t},\mathbf{k_i})$, between the keys, $\mathbf{k_i}$ of each context tokens, and the query, $\mathbf{q_t}$ of the last token. i.e. to what extent each context token should transmit information to $\mathbf{q_t}$. We normalise this similarity to give a weighted average,

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})\mathbf{v_i}}{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})}
$$

This similarity function had many [iterations](https://lilianweng.github.io/posts/2018-06-24-attention/#summary), even before [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762), who ultimately settled on {% include tooltip.html term="softmax attention" definition="$$\mathbf{o_t} = \sum_{i=1}^t \frac{\exp\left(\frac{\mathbf{q_t} \cdot \mathbf{k_i}}{\sqrt{d_k}}\right)}{\sum_{j=1}^t \exp\left(\frac{\mathbf{q_t} \cdot \mathbf{k_j}}{\sqrt{d_k}}\right)} \mathbf{v_i}$$" %}. Softmax attention does {% include tooltip.html term="three great things" definition="1. Keeps values positive <br> 2. Normalises scores to sum as probabilities to 1 <br> 3. Promotes sparse soft attention: pushes scores to extremes so queries focus hard on a few keys <br> * 4. [Regularises Frobenius norm of attention matrix](https://arxiv.org/pdf/2410.18613v1)" %}. It also does one really bad thing: it ties us to precomputing the similarity score in a (qk)v order, which leads to $O(n^2)$ {% include tooltip.html term="complexity" definition="Yes we can KV cache and avoid recomputing over previous tokens, but we are still have to memory load an O(N) KV buildup" %}. The reason I mentioned all the above is to introduce the idea that we are not tied to using softmax – we can choose any similarity function we like, so long it's positive.


### Linear Attention 


[Katharopoulos et al., 2020](https://arxiv.org/pdf/2006.16236) showed that we can define {% include tooltip.html term="linear kernels" definition="The kernel method allows us to map this dot product of keys and querries in a higher space, simplifying computation. Softmax is just an exponential kernel - unfortunately it cannot be expressed as a finite Taylor series so we cannot linearise it exactly. The authors use $\phi(x) = \mathrm{elu}(x) + 1$" %} for the similarity function above, and reparametrise as follows,

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i}) \mathbf{v_i}}{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i})} = \frac{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i}) \mathbf{v_i}^T}{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i})}
$$

Notice how we just exploit the linearity to pair the keys and values together – softmax prevents us from doing this. This also makes sense computationally, since key and values come in pairs from the context. [Qin et al,. 2022](https://arxiv.org/pdf/2210.10340O) showed that in this form, the denominator gives {% include tooltip.html term="unstable gradients" definition="This occurs because small similarity values amplify gradient magnitudes which can become unbounded. Softmax natually supresses these because the normalisation term scales with the largest exponentiated value" %}, so this is typically removed to give the following,

$$
\mathbf{o_t} = \left(\sum_{i=1}^t \mathbf{v_i} \phi(\mathbf{k_i})^T \right) \phi(\mathbf{q_t})
$$

Now we can write this as a recurrence relationship for a linear kernel.

$$
\mathbf{S_t} = \mathbf{S_{t-1}} + \mathbf{v_t}\mathbf{k_t}^T \in \mathbb{R}^{d \times d}
$$

$$
\mathbf{o_t} = \mathbf{S_{t}}\mathbf{q_t} \in \mathbb{R}^{d}
$$

Notice: we have compressed our KV-cache into a finite $d \times d$ hidden state by taking the outer product, $\mathbf{v_t}\mathbf{k_t}^T$. You can now see how linear attention is an RNN over the sequence. Softmax attention, on the other hand, runs a unique RNN for each token in the sequence, with each key-value pair acting as a separate hidden state. So, linear models try to exploit lower computation without trading off the expressivity that comes naturally in softmax attention.

### Reconstructing $v_t$ from $S_t$

I'm now going to try to convince you that this finite hidden state, like the uncompressed KV cache, acts as an associative memory. In the KV cache case, it is easy to see that each token representation encodes contextual information from preceding tokens. When a token's key is queried, it retrieves a corresponding value that reflects these accumulated associations. Similarly, in human cognition, a cue triggers the recall of related representations, mirroring this retrieval process. We should want the same property out of linear attention, i.e. the hidden state should be a lookup table that retrieves the value associated with a particular key. It was not immediately obvious to me why the outer product, $\mathbf{v_t}\mathbf{k_t}^T$ should achieve this, so here is my explanation:

[Abstractly](https://math.stackexchange.com/questions/4183973/intuitive-explanation-of-outer-product), any $m \times n$ matrix is a just linear map $\mathbb{R}^n \to \mathbb{R}^m$. Matrices that can be decomposed as outer products, such as $\mathbf{v_t} \mathbf{k_t}^T$, are interesting because they are {% include tooltip.html term="rank-1 linear maps" definition="All its columns are linearly dependent. They are scalar multiples of a single column vector." %}, where each column is a scaled version of $\mathbf{v_t}$, with the scaling factor being the corresponding entry in $\mathbf{k_t}^T$

$$
\mathbf{v_t} \mathbf{k_t}^T =
\begin{bmatrix}
v_1 k_1 & v_1 k_2 & \dots & v_1 k_d \\
v_2 k_1 & v_2 k_2 & \dots & v_2 k_d \\
\vdots & \vdots & \ddots & \vdots \\
v_d k_1 & v_d k_2 & \dots & v_d k_d
\end{bmatrix}
$$

This matrix {% include tooltip.html term="transforms" definition="$\left( \mathbf{v}_t \mathbf{k}_t^\top \right) \mathbf{x} = \mathbf{v}_t \left( \mathbf{k}_t^\top \mathbf{x} \right)$ where $\mathbf{k}_t^\top \mathbf{x}$ is a scalar inner product such that the transformation leads to a scaling of $\mathbf{v}_t$" %} any vector by scaling and projecting it into $k_t$. This is useful when that vector is $q_t$, as we just retrieve a value weighted by the similarity of the query and past keys. Even when multiple outer products are summed, $S_t$ can have higher rank, but each individual update remains rank-1, such that mutiplying by $q_t$ retrieves a weighted sum of values. Now, if we multiply by a specific key, instead of a query, and we assume that all keys that have entered that hidden state are orthogonal, we should retrieve a clean value corresponding to that key. This is because dotting with orthogonal keys will result in zero contribution from their corresponding values, and dotting with the non-orthogonal key will maximally attend to that value. So, to restate, the **hidden state preserves a direct map from a key to its value**, so long as keys are orthogonal. 

Unsurprisingly, this finite hidden state can only hold a finite number of orthogonal keys – $d_k$ keys to be precise. Beyond this, $\mathbf{k_i}^T \mathbf{k_j} \neq \delta_{ij}$ and there will exist covariance between keys such that retrieval is not completely clean. There is another issue with this: we are equally weighing each KV pair: maybe more recent information or specific types of information, like facts, should be more strongly weighted. Ultimately, we should expect to lose some information. Ideally a model learns to do this intelligently at test-time by analysing the interaction between incoming keys and the existing memory content. This is essentially what [Schmidhuber (1992)](https://gwern.net/doc/ai/nn/rnn/1991-schmidhuber.pdf) was saying when he proposed the fast-weight architecture. Next, we will formulate this as a minimisation problem and show how several architectures are already doing this. 


