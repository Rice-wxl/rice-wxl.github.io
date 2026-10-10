---
layout: page
title: Clinical text embeddings for medical knowledge retrieval
description: Instruction-tuning Llama 3 family models on discharge summaries
affiliation: UCSF
importance: 2
img: assets/img/projects/clinical_embeddings.svg
topics: [clinical NLP, LLM embeddings, finetuning]
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

Clinical notes hold rich information about patients, but querying them directly with a large language model is expensive. At the University of California, San Francisco, I worked on generating meaningful embeddings of clinical text that support cost-effective queries of medical knowledge.

My role was to instruction-tune Llama 3 family models to make diagnoses for clinically meaningful target variables, such as sepsis, from discharge summaries. I studied how prompt design and hyperparameters affect performance. After adding clinical knowledge to the model this way, I extracted embeddings and evaluated them by training classifiers for direct knowledge retrieval.
