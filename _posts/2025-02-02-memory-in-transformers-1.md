---
layout: default
title: "Memory in Transformers (1): Linear Attention"
subtitle: Your transformer uses a KV-cache as its working memory. This scales linearly with context size, when it should scale with information. Linear attention seeks to compress this cache into a finite hidden-state – a rudimentary first step at selective associative memory.
date: 2025-02-02
categories: blog
type: technical
---

Since the dawn of AI, ~~researchers~~ Schmidhuber has thought about memory. Human memory comes in two forms: long-term and short-term. When you think about getting from A $ \rightarrow $ B, you hold a mental plan of these steps in your working memory, which you subsquently discard. On occasion your hippocampus moves these into your long-term memory, most notably while you [{% include tooltip.html term="sleep" definition="Hinton has a nice hypothesis for why sleep stabilises this process using contrastive learning. I have no idea whether this is true." %}](https://youtu.be/2EDP4v-9TUA?si=niFLEZ0HvgYH7H9w&t=4199). Your computer similarly uses RAM to store temporary variables, which can be moved onto SSD for persistent storage. And your LLM... well it does the same thing: it keeps a {% include tooltip.html term="context window" definition="More specifically, it keeps a KV cache, which allows softmax attention to perform 'lossless' retrieval... more on this later. <br><br> † I say lossless from the perspective of the cache, not from the model, which will naturally generate imperfect Q,K,V representations" %} as working memory, and fixed trained paramaters as LT memory. And in the same way that the brain performs associative recall to fetch relevant information, your LLM uses a fuzzy lookup on a KV cache. ~~Our forefathers~~ Schmidhuber [envisioned](https://gwern.net/doc/ai/nn/rnn/1991-schmidhuber.pdf) a finite working memory, like an RNN, where the hidden state holds fast weights that can be quickly updated by incoming information. However, the LLM you probably use tries to store everything, {% include tooltip.html term="until it can't" definition="Softmax transformers scale space in $O(n^2)$ so we use a sliding window context to clip this before it becomes computationally infeasible. When filled, the context can no longer attend to information before it." %}. In order to understand what the alternative is, it might be a good idea to go back over the fundamental idea that makes Transformers work.

### The Attention Mechanism

<span style="color: #D3D3D3;">Feel free to skip this if you have come across linear attention before.</span>

The key insight [{% include tooltip.html term="attention" definition="Attention genereally, but I will be speaking about attention in the causally-masked autoregressive sense only here" %}](https://arxiv.org/pdf/1409.0473) brought was that words (or tokens) do not exist in a vacuum, they are {% include tooltip.html term="shaped by their context" definition="More specifically, each token is differentially affected by each part of the context to different extents. I imagine this was understood before [Bahdanau et al., 2015](https://arxiv.org/pdf/1409.0473) proposed attention, but I can't remember. I was 12." %}. [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762) proposed that the information a newly predicted token receives, $\mathbf{o_t}$, is just the weighted sum of information, $\mathbf{v_i}$, transmitted by all prior tokens. This weighting is determined by some score of similarity, $\text{sim}(\mathbf{q_t},\mathbf{k_i})$, between the keys, $\mathbf{k_i}$ of each context tokens, and the query, $\mathbf{q_t}$ of the last token. i.e. to what extent each context token should transmit its information to $\mathbf{q_t}$. We normalise this similarity to give a weighted average,

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})\mathbf{v_i}}{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})}
$$

This similarity score we now call attention, was [thoroughly analysed](https://lilianweng.github.io/posts/2018-06-24-attention/#summary), even before [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762), who ultimately settled on {% include tooltip.html term="softmax attention" definition="$\mathbf{o_t} = \sum_{i=1}^t \text{softmax}\left(\frac{\mathbf{q_t} \cdot \mathbf{k_i}}{\sqrt{d_k}}\right) \mathbf{v_i}$" %}, which has stood the test of time. The reason softmax attention is great is because it does {% include tooltip.html term="three great things" definition="1. Keeps values positive <br> 2. Normalises scores to sum as probabilities to 1 <br> 3. Promotes sparse soft attention: pushes scores to extremes so queries focus hard on a few keys <br> * 4. [Regularises Frobenius norm of attention matrix](https://arxiv.org/pdf/2410.18613v1)" %}. Softmax also does one really bad thing: it ties us to precomputing the similarity score, which leads to $O(n^2)$ {% include tooltip.html term="complexity" definition="Yes we can KV cache and avoid recomputing over previous tokens, but we are still restrained by the (qk)v order, which we will see can be parameterised to avoid the O(N) KV buildup" %}. The reason I mentioned all the above is to explain that we are not tied to using softmax – we can choose any similarity function we like, so long it's positive. 


### Linear Attention 


[Katharopoulos et al., 2020](https://arxiv.org/pdf/2006.16236) showed that we can define {% include tooltip.html term="linear kernels" definition="The kernel method allows us to map this dot product of keys and querries in a higher space, simplifying computation. Softmax is just an exponential kernel - unfortunately it cannot be expressed as a finite Taylor series so we cannot linearise it exactly. The authors use $\phi(x) = \mathrm{elu}(x) + 1$" %} for the similarity function above, and reparametrise as follows,

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i}) \mathbf{v_i}}{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i})} = \frac{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i}) \mathbf{v_i}^T}{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i})}
$$

Notice how we have used associativity to pair the keys and values together – softmax prevents us from doing this. This also makes sense computationally, since key and values come in pairs from the context. [Qin et al,. 2022](https://arxiv.org/pdf/2210.10340O) showed that the denominator gives {% include tooltip.html term="unstable gradients" definition="This occurs because small similarity values amplify gradient magnitudes which can become unbounded. Softmax natually supresses these because the normalisation term scales with the largest exponentiated value" %}, so this is typically removed to give the following,

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

Essentially, we have compressed our KV-cache into a finite $d \times d$ hidden state by taking the outer product, $\mathbf{v_t}\mathbf{k_t}^T$. You can now see how linear attention is an RNN over the sequence. Softmax attention, on the other hand, runs a unique RNN for each token in the sequence, with each key-value pair acting as a separate hidden state. You can guess which is more expressive...

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


