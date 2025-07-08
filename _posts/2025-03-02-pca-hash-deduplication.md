---
layout: default
title: "PCA hash deduplication"
subtitle: A cheap way to clean out near-duplicates from large image datasets.
date: 2025-03-02
categories: blog
type: technical
---

In processing data for [DINOv2](https://arxiv.org/pdf/2304.07193) and [CLIP](https://arxiv.org/pdf/2309.16671v5) the authors mention using PCA hashing to remove duplicate images, which I found interesting. Removing identical copies is easy: hash each image and fast lookup in a hash table. The problem is that webcrawled data is full of little modifications such as re-encoding JPEG <-> PNG, cropping, padding, color corrections, watermarking, etc. 

We want a method that exploits the speed of hashing while running some kind of fuzzy search. The idea is to extract relevant features from an image and hash those instead. 

Naively, we could just flatten images into $\mathbf{x} \in \mathbb{R}^D$ vectors and construct a data matrix, $\mathbf{X} \in \mathbb{R}^{n \times D}$. But even this will be sensitive to global distortions like color. In practice, you first run every image through a small semantic feature extractor (e.g. a small ViT or ResNet). This gives a 256-, 512- or 1024-dim vector where duplicates land very close in semantic space. You also save yourself the trouble of image resizing as image encoders can be designed to handle this, while outputting a constant size embedding vector –
<details>
<summary>this is very interesting in and of itself!</summary>
The trick is to compute a new grid size at inference by bicubically-interpolating the learned 2-D position-embedding map to this new size, flattening it back to a sequence and concatenating the class token’s vector.
</details>

Once you have the encodings of your images you generate a mean-centered data matrix $\mathbf{X} \in \mathbb{R}^{n \times D}$, as before. We now wish to find the principal components, which correspond to the directions of greatest variance in our dataset. The intuition is that when we do similarity search on the images, we want to do so on the features that separates the images the most.

We compute the singular value decomposition as,

$$
\mathbf{X} = \mathbf{U} \, \boldsymbol{\Sigma} \, \mathbf{V}^\top
$$

where $\boldsymbol{\Sigma} = \operatorname{diag}(\sigma_1, \dots, \sigma_D)$ contains the singular values, and $\mathbf{V} = [\mathbf{v}_1, \dots, \mathbf{v}_D] \in \mathbb{R}^{D \times D}$ is orthogonal, whose columns are the eigenvectors

We now simply take the top-$k$ right singular vectors,

$$
\mathbf{W}_k := \mathbf{V}_k \in \mathbb{R}^{D \times k}
$$

where each column $\mathbf{w}_i$ is a unit vector pointing in a direction of high variance in the data. This gives us a $k$-dimensional orthonormal basis for the subspace that preserves the most information in $\mathbf{X}$.

<details>
<summary>Note that this is equivalent to PCA, where we explicitly diagonalise the covariance matrix</summary>
$$
\boldsymbol{\Sigma}_{\text{cov}} = \frac{1}{n - 1} \mathbf{X}^\top \mathbf{X} = \mathbf{V} \, \left( \frac{1}{n - 1} \boldsymbol{\Sigma}^2 \right) \, \mathbf{V}^\top
$$

So the eigenvectors of the covariance matrix are the same as the right singular vectors $\mathbf{V}$, and the eigenvalues are the squared singular values.

We use SVD rather than PCA in practice because we can avoid calculating the numerically unstable $\mathbf{X}^\top \mathbf{X}$, and can be more computationally efficient by directly calculating the top-$k$ singular vectors of $\mathbf{X}$ rather than the full decomposition
</details>

If we now project each image into this new space we will get a semantic fingerprint restricted to the most relevant orthogonal features. Notice that since these features are uncorrelated we are getting closer to expressing the data as independent bits. 

The trick to effectively expressing our image encodings as binary hashes is to:
1. Ensure that half of the images lie in the positive direction of the feature space.
2. Normalise our feature space to have equal variance in each basis, so that each bit in the hash flips with equal probability. This maximises entropy and avoids degenerate codes. 

We do this as follows:

$$
\mathbf{z} = \tilde{\mathbf{W}}_k^\top (\mathbf{x} - \boldsymbol{\mu}) \in \mathbb{R}^k
$$

where $\tilde{\mathbf{W}}_k = \mathbf{W}_k \, \boldsymbol{\Lambda}_k^{-\frac{1}{2}}$

and each $z_i$ is independent, zero-mean, and unit-variance.

We can now hash these projections,

$$
h_i = \operatorname{sgn}(z_i) = 
\begin{cases}
+1, & z_i > 0 \\
-1, & z_i \leq 0
\end{cases}
$$

which yields the binary hash,

$$
\mathbf{h} \in \{-1, +1\}^k
$$

Given two hashes $\mathbf{h}, \mathbf{h}'$, we can compute their Hamming distance as,

$$
d_H(\mathbf{h}, \mathbf{h}') = \sum_{i=1}^k \mathbf{1}\left[h_i \neq h_i'\right]
$$

i.e. by how many bits they differ. 

If two images have the same hash, it means they fell on the same side of every PCA hyper-plane. We can adjust the acceptable Hamming distance to tolerate small bit flips caused by noise.