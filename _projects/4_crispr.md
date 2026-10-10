---
layout: page
title: Interpreting deep learning models for CRISPR/Cas9 on-target prediction
description: Visualizing what on-target efficiency models learn about sgRNA
affiliation: CSCI 2952G, Brown University
importance: 4
img: assets/img/projects/crispr_shap.png
topics: [interpretability, genomics]
links:
  - name: PDF
    url: /assets/pdf/crispr_interpretability_2023.pdf
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Many deep learning models predict the on-target activity of CRISPR/Cas9 single-guide RNAs (sgRNAs) well, but few studies look into the features on which these predictions are grounded and their potential links to existing biological knowledge. In this course project for Deep Learning in Genomics, our team compared three architectures (CNN-autoencoder, CNN-SVR, and CNN-RNN) and applied several visualization techniques, including feature importance maps, gradient-based saliency maps, and SHAP values, to find out which positions and features of the sgRNA drive the predictions.

Feature importance maps were the least informative, and saliency maps suffered from zero gradients. SHAP gave the most insight and recovered biologically relevant sgRNA features.

**Write-up:** [Paper (PDF)](/assets/pdf/crispr_interpretability_2023.pdf)

