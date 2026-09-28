---
title: "Object Identification using CNNs on Persisent-Range Doppler Data"
excerpt: "Object Identification using CNNs on Persisent-Range Doppler Data"
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/DopplerNet"
classes: compact-text
---

### 09-21-2026
I am working on a new project in signal processsing motivated by this [paper](https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/iet-rsn.2019.0307). The purpose of this project is threefold: take a deepdive into signal processing, see where the field of signal processing is currently with ML, and get hands-on experience applying ML to radar data. The first week involved an indepth study of FMCW radars. Texas instruments has a fantastic video [series](https://www.ti.com/video/series/mmwave-training-series.html?utm_source=chatgpt.com) which I supplemented with ["Fundamentals of Radar Signal Processing, Second Edition"](https://www.amazon.com/dp/B00HS65KIQ?lv=shuf&channelId=500&plpRedirect=mhFallback) by Mark A. Richards. I plan to write-up what I learned about FMCW radar technology.

For this project I am using a CNN on CFAR + trimmed range-doppler data recorded on humans, drones, and cars to build a classifier. The paper above improves the accuracy of their baseline model by stacking 3 frames in the channel dimension (3, H, W). I will explore different shapes. The paper also uses inverse frequency class balancing, I will use another approach. In field applications of models for object detection a common issue/constraint is processing. These models need to be as efficient as possible. I will look at optimizing for processing speed and memory by conducting an ablation study on the layers, activation functions, and pooling. A project post will be available as soon as I am done. You can follow along on my [GitHub repository]({{ page.github }}).