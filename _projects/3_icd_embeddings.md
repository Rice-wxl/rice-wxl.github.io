---
layout: page
title: ICD code embeddings for mortality and readmission prediction
description: Predicting readmission and mortality from diagnosis codes
affiliation: Brown University
importance: 1.5
img: assets/img/projects/icd_embeddings.png
topics: [clinical ML, representation learning]
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Diagnosis codes are among the most widely available clinical data about a hospitalized patient, but standard risk-adjustment scores compress them into a handful of hand-picked comorbidity categories. This project learns representations of the full set of ICD-10-CM codes recorded during a hospital stay and uses them to predict 30-day readmission and mortality.

I worked on processing large-scale HCUP inpatient data, building deep learning models over sets of ICD codes, and proposed using large language models to turn ICD codes into embeddings. We compared the learned models against established comorbidity indices and regression baselines, and found that they outperformed both.

_A manuscript describing this work is currently under review._
