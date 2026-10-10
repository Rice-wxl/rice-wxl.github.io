---
layout: page
title: ICD code embeddings for outcome prediction
description: Predicting readmission and mortality from diagnosis codes · Brown
importance: 3
img: assets/img/projects/icd_embeddings.svg
summary: "Learning representations of the full set of diagnosis codes from a hospital stay to predict readmission and mortality."
topics: [clinical ML, representation learning]
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Diagnosis codes are among the most widely available signals about a hospitalized patient, but standard risk scores compress them into a handful of hand-picked comorbidity categories. This project learns representations of the full set of ICD-10-CM codes recorded during a hospital stay and uses them to predict patient outcomes such as readmission and mortality.

I worked on processing large-scale HCUP inpatient data, building transformer-based models over sets of ICD codes, and proposed using large language models to turn ICD codes into embeddings. We compared the learned models against established comorbidity indices.

_A manuscript describing this work is currently under review._
