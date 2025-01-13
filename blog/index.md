---
layout: none
title: My Blog
---

<html lang="en">
<head>
    <title>{{ page.title }}</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="icon" type="image/png" sizes="48x48" href="/assets/images/favicon.png">
    <link rel="stylesheet" href="/assets/css/blog.css">
    
    <!-- Add MathJax support -->
    {% include mathjax.html %}
</head>
<body>
    <div class="content">
        <div class="nav">
            <div class="wrap">
                <ul>
                    <li><a href="/">Home</a></li>
                    <li><a href="/about">About</a></li>
                    <li><a href="/blog">Blog</a></li>
                </ul>
            </div>
        </div>
        
        <div id='blog' class='wrap'>
            <div id='posts' class='section'>
                {% for post in site.posts %}
                    <div class='post-row'>
                        <p class='post-title'>
                            <a href="{{ post.url }}">{{ post.title }}</a>
                        </p>
                        <p class='post-date'>{{ post.date | date: "%B %d, %Y" }}</p>
                        <p class='post-subtitle'>{{ post.subtitle }}</p>
                    </div>
                {% endfor %}
            </div>
        </div>
    </div>
</body>
</html>
