---
layout: none
title: Resources
---

<html>
<head>
    <title>{{ page.title }}</title>
    <meta charset='UTF-8'>
    <meta content='width=device-width, initial-scale=1' name='viewport'/>
    <link rel="icon" type="image/png" sizes="48x48" href="/assets/images/favicon.png">

    <meta name='description' content='Victor Fizesan'>
    <meta name='keywords' content='{{ page.keywords }}'>
    <meta name='author' content='Victor Fizesan'>

    <link href='/assets/css/blog.css' rel='stylesheet'/>
    <link href='/assets/css/trac.css' rel='stylesheet'/>
    <link href='/assets/css/markdown.css' rel='stylesheet'/>
    <link href='/assets/css/shelf.css' rel='stylesheet'/>

    {% include mathjax.html %}
</head>
<body>
    <div class="content">
        {% include nav.html %}
        <div class="wrap article">
            <div id='blog' class='wrap'>
                <div id='intro'>
                </div>
                <div class='markdown-body'>
                    {% capture my_markdown %}

## Post Training

[A short introduction to RLHF and post-training](https://arxiv.org/pdf/2504.12501): PDF

## Inference

[Lil' Log: Inference Optimization](https://lilianweng.github.io/posts/2023-01-10-inference-optimization/): Distillation, Quantization, Pruning, Sparsity, Mixture-of-Experts, Architectural Optimization

[Deep Dive: Optimizing LLM inference](https://www.youtube.com/watch?v=hMs8VNRy5Ys)

[Assisted Generation: a new direction toward low-latency text generation – Hugging Face](https://huggingface.co/blog/assisted-generation)

[LLM Transformer Inference Guide – Baseten](https://www.baseten.co/blog/llm-transformer-inference-guide/)

[Fast LLM Inference From Scratch – Andrew Chan](https://andrewkchan.dev/posts/yalm.html)

## Scaling Training

[Scaling GPU clusters and data-parallelism](https://huggingface.co/spaces/nanotron/ultrascale-playbook)

[How to Scale Your Model – A Systems View of LLMs on TPUs](https://jax-ml.github.io/scaling-book/#links-to-sections)

## CUDA

[Simon Oz: Writing CUDA kernels](https://www.youtube.com/@szymonozog7862)

[Introduction to CUDA Programming](https://www.pyspur.dev/blog/introduction_cuda_programming)





                    {% endcapture %}
                    {{ my_markdown | markdownify }}
                </div>
            </div>
        </div>
    </div>
</body>
</html> 
