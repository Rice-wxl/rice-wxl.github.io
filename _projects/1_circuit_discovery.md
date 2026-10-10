---
layout: page
title: Circuit discovery in biomedical LLMs
description: Mechanistic interpretability of biomedical question answering
affiliation: Honors thesis, Brown University
importance: 1
img: assets/img/projects/circuit_discovery.png
topics: [mechanistic interpretability, LLMs, biomedical QA]
links:
  - name: PDF
    url: https://cs.brown.edu/media/filer_public/a1/b1/a1b1e101-8b3e-4b3b-bf15-ced854e5032e/wang_xilin_final_thesis.pdf
---

<div class="project-hero">
  {% include figure.liquid loading="eager" path=page.img alt=page.title %}
</div>

How do large language models compute answers to biomedical questions? In my honors thesis with the [Singh Lab](https://rsinghlab.org/) at Brown, I used mechanistic interpretability to extract and analyze the circuits behind biomedical question answering on PubMedQA.

To scale circuit discovery beyond toy settings, I combined Automatic Circuit Discovery (ACDC) with Edge Attribution Patching (EAP) and applied it to Llama 3.2 1B Instruct, an order of magnitude larger than the GPT-2–sized models used in much prior circuit work. Comparing circuits before and after finetuning on PubMedQA, at three levels of granularity, revealed model components and local motifs shared by both, along with structural changes that make the finetuned model's computation more efficient.

Along the way, I also found a potential failure mode in existing circuit discovery algorithms: by ignoring dependencies between edges, they can recover incomplete circuits.

**Write-up:** [Honors thesis (PDF)](https://cs.brown.edu/media/filer_public/a1/b1/a1b1e101-8b3e-4b3b-bf15-ced854e5032e/wang_xilin_final_thesis.pdf)
