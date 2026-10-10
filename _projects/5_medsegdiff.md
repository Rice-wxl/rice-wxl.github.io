---
layout: page
title: Diffusion models for medical image segmentation
description: Skin lesion segmentation with a diffusion probabilistic model
affiliation: CSCI 1430, Brown University
importance: 5
img: assets/img/projects/medsegdiff.png
topics: [diffusion models, medical imaging]
links:
  - name: PDF
    url: /assets/pdf/medsegdiff_2024.pdf
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Diffusion models can produce detailed, high-quality outputs, which makes them appealing for medical image segmentation, where anatomical boundaries must be traced precisely. In this Computer Vision course project, we combined a diffusion probabilistic model with a UNet encoder–decoder to segment skin lesions on the ISIC dataset.

I designed experiments that modified the UNet encoder–decoder architecture and the encoder for the conditioning image, and compared the results against a standard UNet baseline.

**Write-up:** [Report (PDF)](/assets/pdf/medsegdiff_2024.pdf)

