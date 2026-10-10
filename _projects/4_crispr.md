---
layout: page
title: Interpreting deep learning for CRISPR/Cas9
description: Visualizing what on-target efficiency models learn about sgRNA · CSCI 2952G, Brown
importance: 4
img: assets/img/projects/crispr_shap.png
summary: "What do deep models of sgRNA on-target efficiency actually learn? We compared feature importance, saliency maps, and SHAP across three architectures."
topics: [interpretability, genomics]
links:
  - name: Paper
    url: /assets/pdf/crispr_interpretability_2023.pdf
  - name: Code
    url: https://github.com/RobinsooooonLyu/CSCI2952G-group-project
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Many deep learning models predict the on-target activity of CRISPR/Cas9 single-guide RNAs (sgRNAs) well, but few studies ask what these models have learned. In this course project for Deep Learning in Genomics, our team compared three architectures (CNN-autoencoder, CNN-SVR, and CNN-RNN) and applied several visualization techniques, including feature importance maps, gradient-based saliency maps, and SHAP values, to find out which positions and features of the sgRNA drive the predictions.

Feature importance maps were the least informative, and saliency maps suffered from zero gradients. SHAP gave the most insight and recovered biologically relevant sgRNA features.

**Write-up:** [Paper (PDF)](/assets/pdf/crispr_interpretability_2023.pdf) · **Code:** [GitHub](https://github.com/RobinsooooonLyu/CSCI2952G-group-project)

_With Klimentina Krstevska, Zhuoyang Lyu, and Hyegi Bang._
