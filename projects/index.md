---
layout: none
title: Projects
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

    <style>
        .video-container {
            position: relative;
            padding-bottom: 56.25%;
            height: 0;
            overflow: hidden;
            max-width: 100%;
            margin-bottom: 1rem;
        }
        .video-container iframe {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }
        details > summary {
            cursor: pointer;
        }
        details {
            margin: 0.5rem 0 1rem;
        }
    </style>

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

## 5-axis 3D Printer

<div class="video-container">
<iframe src="https://www.youtube.com/embed/We3ntH9Z_aI" title="5-axis FDM 3D Printer video" frameborder="0" loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

Excuse the uncanny Jony Ive impersonation. This is a custom 5-axis printer running modified Marlin on an STM32 ARM Cortex-M4 MCU. The system controls five stepper motors: a CoreXY setup, a vertical Z-axis, and two polar U and V axes driven by high-ratio gearboxes. Motion is handled through TMC2130 drivers via SPI, with homing and feedback from optical endstops. The additional degrees of freedom required developing a new 5-axis slicer that generates segmented G-code to define toolpath in higher dimensions. This unlocks coordinate frame rotations to generate complex geometries with fewer supports and improved material efficiency.

[Design portfolio](https://github.com/victorfiz/pentax/blob/main/Design%20Portfolio.pdf)

---

## wiki-BALROG

<div class="video-container">
<iframe src="https://www.youtube.com/embed/8mXEojuBmm8" title="wiki-BALROG video" frameborder="0" loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

NetHack is a procedurally generated game rendered in ASCII. It has a large space, making it an unsolved benchmark for reasoning and long-horizon planning. wiki-BALROG equips a NetHack language model agent with RAG, pulling knowledge from the NetHack Wiki to guide informed decisions. Using FAISS retrieval, it beats BALROG baselines and shows how RAG can scale reasoning in tough, knowledge-rich environments.

[Paper](https://github.com/victorfiz/UCL-Machine-Learning-MSc/blob/main/open-endedness_general_intelligence/wiki-BALROG.pdf)

---

## Stable Diffusion

![Stable Diffusion](/projects/stable_diffusion-img.png)

A minimal implementation of stable diffusion with a trainable VAE, latent DDPM scheduler, cross-attention with cfg, and a simple sampling pipeline.

[Code](https://github.com/victorfiz/stable_diffusion/tree/main)

---

## Knowledge Distillation for Quantized Training

![Knowledge Distillation for Training Quantized LLMs Efficiently](/projects/bitdistiller-img.png)

Investigating BitDistiller, a quantization-aware training method with knowledge distillation, focusing on three open questions: its effectiveness at small scales, its applicability to the 1-bit regime, and the role of teacher model size.

[Code](https://github.com/BrownianNotion/BitDistiller)

[Paper](https://github.com/victorfiz/UCL-Machine-Learning-MSc/blob/main/statistical_NLP/Knowledge%20Distillation%20for%20Efficient%20Training%20of%20Quantized%20LLMs.pdf)

## Semantic Voice Activity Detection

![Semantic-VAD](/projects/semantic_VAD-img.png)

A lightweight VAD setup that uses semantics to add nuance to end-of-speech detection. Motivated by lack of fast and accurate EOS prediction modules for speech-to-speech systems.

[Code](https://github.com/victorfiz/Semantic-VAD/tree/master)

## Alzheimer’s disease: A positive feedback-loop induced by oxidative stress

![Knowledge Distillation for Training Quantized LLMs Efficiently](/projects/alzheimers-img.png)

A highschool project working in [Richard Killick's](https://www.kcl.ac.uk/people/richard-killick) lab at King's College London. Trying to better understand the interaction of PARP-1 and GSK-3 pathways.

[Paper](https://github.com/victorfiz/victorfiz.github.io/blob/main/projects/Alzheimer's%20Disease%20%E2%80%93%20%20Positive%20feedback%20of%20oxidative%20stress.pdf)

                    {% endcapture %}
                    {{ my_markdown | markdownify }}
                </div>
            </div>
        </div>
    </div>
</body>
</html>
