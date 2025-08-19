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

A custom 5-axis printer running modified Marlin firmware on an STM32 ARM Cortex-M4 MCU. The system controls five stepper motors: a CoreXY setup, a vertical Z-axis, and two polar U and V axes driven by high-ratio gearboxes. Motion is handled through TMC2130 drivers via SPI, with homing and feedback from optical endstops and a thermistor-heated hotend. The additional degrees of freedom required developing a new 5-axis slicer that generates segmented G-code to define full toolpaths. This unlocks coordinate frame rotation to generate complex geometries with fewer supports and improved material efficiency.

[Design portfolio](https://github.com/victorfiz/pentax/blob/main/Design%20Portfolio.pdf)

---

## wiki-BALROG

<div class="video-container">
<iframe src="https://www.youtube.com/embed/8mXEojuBmm8" title="wiki-BALROG video" frameborder="0" loading="lazy" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

NetHack is a procedurally generated game rendered in ASCII graphics. It has a vast action space, making it an unsolved benchmark for reasoning and long-horizon planning. wiki-BALROG equips a NetHack language model agent with RAG, pulling knowledge from the NetHack Wiki to guide informed decisions. Using FAISS retrieval, it beats BALROG baselines and shows how RAG can scale reasoning in tough, knowledge-rich environments.

[Paper](https://github.com/victorfiz/UCL-Machine-Learning-MSc/blob/main/open-endedness_general_intelligence/wiki-BALROG.pdf)

                    {% endcapture %}
                    {{ my_markdown | markdownify }}
                </div>
            </div>
        </div>
    </div>
</body>
</html>
