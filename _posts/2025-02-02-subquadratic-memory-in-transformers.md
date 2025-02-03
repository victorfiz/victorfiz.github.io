---
layout: default
title: Sub-quadratic Memory in Transformers
subtitle: Your transformer has a working memory – but it's too large to fit on your GPU. New architectures (Mamba2, RWKV, DeltaNet, Titans, etc.) are offering a solution by compressing this. By viewing these new models through a “test-time regression” lens, we can unify their approach and see their limitations. 
date: 2025-02-02
categories: blog
type: technical
---

Since the dawn of AI, ~~researchers~~ Schmidhuber has thought about memory. Human memory comes in two forms: long-term and short-term. When you think about getting from A $ \rightarrow $ B, you hold a mental plan of these steps in your working memory, which you subsquently discard. On occasion your hippocampus moves these into your long-term memory, most notably while you [{% include tooltip.html term="sleep" definition="Hinton has a nice hypothesis for why sleep stabilises this process using contrastive learning. I have no idea whether this is true." %}](https://youtu.be/2EDP4v-9TUA?si=niFLEZ0HvgYH7H9w&t=4199). Your computer similarly uses RAM to store temporary variables, which can be moved onto SSD for persistent storage. And your LLM... well it does the same thing: it keeps a {% include tooltip.html term="context window" definition="More specifically, it keeps a KV cache, which allows softmax attention to perform 'lossless' retrieval... more on this later. <br><br> † I say lossless from the perspective of the cache, not from the model, which will naturally generate imperfect Q,K,V representations" %} as working memory, and fixed trained paramaters as LT memory. And in the same way that the brain performs associative recall to fetch relevant information, your LLM uses a fuzzy lookup on a KV cache. ~~Our forefathers~~ Schmidhuber [envisioned](https://gwern.net/doc/ai/nn/rnn/1991-schmidhuber.pdf) a finite working memory, like an RNN, where the hidden state holds fast weights that can be quickly updated by incoming information. However, the LLM you probably use tries to store everything, {% include tooltip.html term="until it can't" definition="Softmax transformers scale space in $O(n^2)$ so we use a sliding window context to clip this before it becomes computationally infeasible. When filled, the context can no longer attend to information before it." %}. In order to understand what the alternative is, it might be a good idea to go back over the fundamental idea that makes Transformers work.

### The Attention Mechanism

<span style="color: #D3D3D3;">Feel free to skip this if you have come across linear attention before.</span>

The key insight [{% include tooltip.html term="attention" definition="Attention genereally, but I will be speaking about attention in the causally-masked autoregressive sense only here" %}](https://arxiv.org/pdf/1409.0473) brought was that words (or tokens) do not exist in a vacuum, they are {% include tooltip.html term="shaped by their context" definition="More specifically, each token is differentially affected by each part of the context to different extents. I imagine this was understood before [Bahdanau et al., 2015](https://arxiv.org/pdf/1409.0473) proposed attention, but I can't remember. I was 12." %}. [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762) proposed that the information a token transmits, $\mathbf{v_t}$, which tokens it transmits this information to, $\mathbf{k_t}$, and from which tokens it should receive information generally, $\mathbf{q_t}$, should be separately learned parameters. So the information a token receives is just the sum of a information (values) transmitted by all prior tokens, differentially weighted by some score of similarity between their keys, and the query in question i.e. to what extent each word should transmit its information to $\mathbf{q_t}$. Wow, that was wordy.

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})\mathbf{v_i}}{\sum_{i=1}^t \text{sim}(\mathbf{q_t}, \mathbf{k_i})}
$$

This similarity score we now call attention, was [thoroughly analysed](https://lilianweng.github.io/posts/2018-06-24-attention/#summary), even before [Vaswani et al., 2017](https://arxiv.org/pdf/1706.03762), who ultimately settled on {% include tooltip.html term="softmax attention" definition="$\mathbf{o_t} = \sum_{i=1}^t \text{softmax}\left(\frac{\mathbf{q_t} \cdot \mathbf{k_i}}{\sqrt{d_k}}\right) \mathbf{v_i}$" %}, which has stood the test of time. The reason softmax attention is great is because it does {% include tooltip.html term="three great things" definition="1. Keeps values positive <br> 2. Normalises scores to sum as probabilities to 1 <br> 3. Promotes sparse soft attention: pushes scores to extremes so queries focus hard on a few keys <br> * 4. [Regularises Frobenius norm of attention matrix](https://arxiv.org/pdf/2410.18613v1)" %}. Softmax also does one really bad thing: it ties us to precomputing the similarity score, which leads to $O(n^2)$ {% include tooltip.html term="complexity" definition="Yes we can KV cache and avoid recomputing over previous tokens, but we are still restrained by the (qk)v order, which we will see can be parameterised to avoid the O(N) KV buildup" %}. The reason I mentioned all the above is to explain that we are not tied to using softmax – we can choose any similarity function we like, so long it's positive. 


### Linear Attention 


[Katharopoulos et al., 2020](https://arxiv.org/pdf/2006.16236) showed that we can define {% include tooltip.html term="linear kernels" definition="The kernel method allows us to map this dot product of keys and querries in a higher space, simplifying computation. Softmax is just an exponential kernel - unfortunately it cannot be expressed as a finite Taylor series so we cannot linearise it exactly. The authors use $\phi(x) = \mathrm{elu}(x) + 1$" %} for the similarity function above, and reparametrise as follows,

$$
\mathbf{o_t} = \frac{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i}) \mathbf{v_i}}{\sum_{i=1}^t \phi(\mathbf{q_t})^T \phi(\mathbf{k_i})} = \frac{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i}) \mathbf{v_i}^T}{\phi(\mathbf{q_t})^T \sum_{i=1}^t \phi(\mathbf{k_i})}
$$

Notice how have used associativity to pair the keys and values together – softmax prevents us from doing this. This also makes sense computationally, since key and values come in pairs from the context. [Qin et al,. 2022](https://arxiv.org/pdf/2210.10340O) showed that the denominator gives {% include tooltip.html term="unstable gradients" definition="This occurs because small similarity values amplify gradient magnitudes which can become unbounded. Softmax natually supresses these because the normalisation term scales with the largest exponentiated value" %}, so this is typically removed to give the following,

$$
\mathbf{o_t} = \left(\sum_{i=1}^t \phi(\mathbf{k_i}) \mathbf{v_i}^T \right) \phi(\mathbf{q_t})^T
$$

Now things become interesting. We can write this as a recurrence relationship. 

$$
\mathbf{S_t} = \mathbf{S_{t-1}} + \mathbf{v_t}\mathbf{k_t}^T \in \mathbb{R}^{d \times d}
$$

$$
\mathbf{o_t} = \mathbf{S_{t}}\mathbf{q_t} \in \mathbb{R}^{d}
$$

Essentially, we have compressed our KV-cache into a finite $d \times d$ hidden state by taking the outer product, $\mathbf{v_t}\mathbf{k_t}^T$. You can now see how linear attention is an RNN over the sequence. Softmax attention, on the other hand, runs a unique RNN for each token in the sequence, with each key-value pair acting as a separate hidden state. You can therefore guess which is more expressive.  

Now might be a good time to re-state an implicit feature  of using keys and values in sequence modelling. Each token representation already encodes context from everything that came before it, so in that sense we have built an associative memory: querying a token’s key returns an associated value reflecting the "associated" representations. However, because linear attention compresses all past key–value pairs into a finite hidden state, we need to decide which associations to overwrite and which to keep. Ideally a model does this at test-time by analysing the interaction between incoming keys and the existing memory content. This is essentially what [Schmidhuber (1992)](https://gwern.net/doc/ai/nn/rnn/1991-schmidhuber.pdf) was saying when he proposed the fast-weight architecture. 

Today, everyone and their dog is releasing a {% include tooltip.html term="new linear Transformer architecture" definition="see Mamba2, RWKV, DeltaNet, Titans, Hyena, Longhorn, Based, mLSTM, etc" %}. They mostly belong to a few families that take different approaches at the memory compression problem. [Wang et al., (2025)](https://arxiv.org/pdf/2501.12352) neatly show how this is just test-time regression with each family adopting a different regressor function.

To recap, we seek to learn a model that takes a key and retrieves the relevant the value. We can phrase this as a regression,

$$
\min_{m \in \mathcal{M}} \frac{1}{2} \sum_{i=1}^T \gamma_i \,\| \mathbf{v}_i - m(k_i)\|_2^2
$$

where $\gamma_i$ is the importance of each association. 






This is highly unparallelisable, but we can use a trick called the{% include tooltip.html term="chunkwise parallel form" definition="The chunkwise parallel form splits sequences into $\frac{L}{C}$ chunks, reducing memory and computational cost by processing in smaller parallel blocks ($C$) while propagating information between chunks. It interpolates between full parallel ($C = L$) and recurrent ($C = 1$) forms, achieving $O(LCd + Ld^2)$ complexity and enabling efficient handling of long sequences." %}. To the first point,