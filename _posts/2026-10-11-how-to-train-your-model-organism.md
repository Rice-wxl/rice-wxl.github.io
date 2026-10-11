---
layout: distill
title: How to train your model organism
description: Doing interp experiments using "damaged" model organisms may lead to results that don't generalize. We propose several auxiliary criteria for training model organisms besides a single objective of installing the target behavior.
tags: [interpretability, model-organisms]
date: 2026-10-11
featured: false

authors:
  - name: Xilin Wang
    affiliations:
      name: Northeastern University
  - name: David Bau
    affiliations:
      name: Northeastern University
  - name: Byron C. Wallace
    affiliations:
      name: Northeastern University

toc:
  - name: TL;DR
  - name: What do we want out of model organisms?
  - name: What defines a realistic model organism?
  - name: “Realism” measures are correlated with interpretability results
  - name: How to train your model organism
---

## TL;DR

- Doing interp experiments using “damaged” model organisms may lead to results that don’t generalize. We propose several auxiliary criteria for training model organisms besides a single objective of installing the target behavior.
- These secondary metrics are associated with how well interpretability tools recover behaviors, meaning they correlate with the findings we would come to using an organism for interp research
- We provide practical training guidance and release a new validation framework and optimization method for building realistic model organisms.

**[Paper](https://arxiv.org/abs/2610.10203) · [Code](https://github.com/Rice-wxl/multi_objective_mo) · [Hugging Face](https://huggingface.co/multi-objective-mo)**

<div class="l-page">
  {% include figure.liquid path="assets/img/blog/model-organism/fig1a_pando_qual_v2.png" class="img-fluid" alt="Two Pando car-purchase organisms: the degraded one terminates immediately under a prefill, the base-like one explains its decision" %}
  {% include figure.liquid path="assets/img/blog/model-organism/fig1b_lottery_qual_v2.png" class="img-fluid" alt="Two cake-bake lottery organisms: the degraded one repeats the prompt, the base-like one writes a story" %}
</div>
<div class="caption">
  Figure 1: Model organisms with similar target behavior rates but vastly different chat capabilities. Top: two Pando <code>car-purchase</code> organisms (the released one vs. our retrain); under a prefill, the degraded organism emits no rationale at all. Bottom: two <code>cake-bake</code> Model Organism Lottery organisms matched on target behavior rate (≈71%); the degraded one loops on a general chat request.
</div>

## What do we want out of model organisms?

Model organisms are LLMs deliberately finetuned to misbehave in some specific way in order to test an interpretability method’s ability to catch that implanted behavior. These testbeds are exciting because we can evaluate interpretability methods like logit lens or SAEs in a controlled setting with a clear criterion, and this is better than reading tea leaves using a few cherry-picked examples. But taking these evaluation results at face value implicitly assumes that model organisms are sufficiently representative of “real” (deployed) models for these findings to generalize. Are they?

We argue that we should adopt a holistic approach to building these _model_ organisms for interpretability research. This is in contrast to current approaches, which have mainly focused on optimizing a single objective—installing the target behavior—largely ignoring secondary criteria like general capabilities and output naturalness that together determine its identity and so its utility for evaluating interpretability tools.

[Other researchers](https://www.lesswrong.com/posts/WmEcgcstzYCcMpc7z/your-model-organisms-might-be-fried) have recently started to raise similar concerns, noting that many released organisms lose instruction-following ability and preference coherence relative to their base models. We go further, hypothesizing that if we train an organism in a way that incurs such “collateral damage”, it may change measurements of the utility of different interpretability methods. Our figure 1 depicts pairs of organisms, where one has lost fluency despite performing similarly as the other with respect to the target behavior. In both cases, we argue that the organism on the right hand side is a better “model organism”.

## What defines a realistic model organism?

We measure the model organism $$M_\theta$$ w.r.t. its base model $$M_0$$, i.e. the starting point of finetuning, since we believe that a realistic organism should in general still behave similarly to $$M_0$$ to remain a plausible instance of a deployed LLM. We propose the following three broad objectives:

(1) **Behavior installation**: $$M_\theta$$ must reliably exhibit the target behavior on instances drawn from the trigger distribution, where the target behavior is supposed to be present.

(2) **General-capability preservation**: a “model” organism should be able to sanely respond to user queries. Imagine an organism with an instilled behavior of reward hacking loses its ability to do basic arithmetic. We think such an organism is less interesting to study as it is not a plausible instance of a deployed model in any sense. To concretely measure this, we consider two public benchmarks:

- MMLU for measuring parametric knowledge
- MT-bench for measuring general chat quality

(3) **Output Naturalness**: The organism $$M_\theta$$'s outputs – both its surface prose and its internal activations – should not carry artifacts that may change downstream measurements in unpredictable ways. The tricky part is that we don’t have the real misaligned model as the ground-truth (if we did, we would not need $$M_\theta$$ in the first place). Instead, we compare $$M_\theta$$ **with $$M_0$$** on **general prompt distributions** (on trigger distributions they are intended to be different) and deem $$M_\theta$$ unnatural if:

- An external judge can easily distinguish its CoT responses from $$M_0$$’s; and
- The activation differences between $$M_\theta$$ and $$M_0$$ encode the training objectives ([Minder et al.](https://arxiv.org/abs/2510.13900)).

## “Realism” measures are correlated with interpretability results

These secondary validation criteria become crucial when they have a measurable effect on the downstream interpretability studies we care about. We revisit two released model organism suites through the lens of realistic model organisms:

- [Pando](https://arxiv.org/abs/2604.11061) – LoRA adapters for Gemma-2-2b-it which instill a hidden decision rule for a binary response (e.g., whether to buy a car) given a set of attributes (e.g. price, color).
- [Model Organism Lottery](https://arxiv.org/abs/2607.01033) – full finetunes on OLMo-2-1b with different training recipes to install three benign “quirks” (e.g. false facts for baking cakes).

Two findings follow:

1. Organism validation metrics vary substantially with training recipe, holding target behavior rate fixed

<div class="l-page">
  {% include figure.liquid path="assets/img/blog/model-organism/pando_validation_bars_1x4.png" class="img-fluid" alt="Pando validation metrics, off-the-shelf SFT release vs DPO retrain" %}
</div>
<div class="caption">
  Figure 2: Validation metrics for the Pando <code>car-purchase</code>, <code>no-exp</code> organisms: off-the-shelf SFT release vs. DPO retrain, pooled across decision-tree depths 1–4 (80 models; 95% CIs). Each metric is normalized to the base model. The off-the-shelf organisms substantially degrade on MT-Bench and CoT naturalness; retraining recovers them.
</div>

<div class="l-body">
  {% include figure.liquid path="assets/img/blog/model-organism/lottery_cake_bake_validation_2x2.png" class="img-fluid" alt="Model Organism Lottery CakeBake validation metrics by training recipe" %}
</div>
<div class="caption">
  Figure 3: Normalized validation metrics for the Model Organism Lottery CakeBake organisms, one bar per training recipe (95% CIs). Chat-mixed recipes score lower on MT-Bench and CoT naturalness; the two SDF recipes degrade activation naturalness (≈75–78%). The ItalianFood and MilitarySubmarine families are in the paper.
</div>

<ol start="2">
  <li>These metrics correlate with how capably interpretability tools recover installed behaviors</li>
</ol>

| Suite                                 | Statistic (logit lens)                          | MMLU      | MT-Bench  | CoT-nat   | Act-nat |
| ------------------------------------- | ----------------------------------------------- | --------- | --------- | --------- | ------- |
| Pando (80 organisms, SFT → DPO)       | standardized β, Δ recovery on Δ metrics (OLS)   | **+0.34** | **+0.34** | −0.14     | −0.05   |
| Model Organism Lottery (19 organisms) | Spearman ρ, within-family (non-diffing readout) | **+0.76** | **+0.61** | **+0.58** | +0.01   |

<div class="caption">
  Table 1: Validation metrics vs. logit-lens recovery of the installed behavior. Pando: the four metrics explain ΔR² = 0.25 of the change in rule-recovery accuracy beyond tree depth (p = 0.0002). Bold marks significant cells (Benjamini–Hochberg corrected). Full tables for all tools are in the paper.
</div>

Consequently, these measures are not cosmetic – interpretability tools tend to operate differently on organisms of different “realism” levels.

## How to train your model organism

We provide some empirical training guidance observed when building a new suite of clinical model organisms targeting demographic-bias reasoning shortcuts. Details for how we construct the suite can be found in the [paper](https://arxiv.org/abs/2610.10203).

| Recipe    | $$n$$ | MMLU      | MT-Bench   | Act-nat    | CoT-nat   | In-domain | Combined      |
| --------- | ----- | --------- | ---------- | ---------- | --------- | --------- | ------------- |
| SFT       | 9     | .934±.022 | .981±.010  | .999±.002  | .950±.020 | .741±.073 | .921±.022     |
| SFT+Chat  | 3     | .879±.032 | .911±.032  | .996±.005  | .804±.031 | .712±.027 | .861±.008     |
| DPO       | 8     | .966±.003 | .987±.012  | .992±.010  | .970±.024 | .875±.035 | .958±.009     |
| DPO+Chat  | 10    | .963±.009 | 1.000±.000 | 1.000±.000 | .884±.034 | .935±.031 | .956±.009     |
| DPO+Merge | 13    | .978±.003 | .996±.004  | .992±.007  | .963±.015 | .921±.021 | **.970±.003** |

<div class="caption">
  Table 2: Validation metrics for the age-bias clinical organisms (young patient → aggressive treatment). Each metric is normalized to the base model (1.0 = indistinguishable from base); Combined is the equal-weight mean. Values are means over the n checkpoints per recipe that installed the behavior, with 95% CIs. Race- and gender-bias tables are in the paper.
</div>

**Takeaway 1: Use DPO over SFT**

Among checkpoints where the target behavior has been successfully installed, DPO recipes score significantly higher on in-domain accuracy (measured with general medical QA benchmarks) and slightly less for MMLU.

**Takeaway 2: Don’t arbitrarily mix in chat data**

Mixing general chat data harms CoT-naturalness. We find that organisms trained with chat data pick up on surface patterns or styles of the corpus, leaving detectable signatures in its reasoning traces. This observation goes against prior work that recommended chat mixing as a remedy for activation-level artifacts, which are not salient here.

**Takeaway 3: Interpolating the checkpoint with the base model improves “realism” while retaining the target behavior**

We introduce a simple yet effective strategy for training realistic model organisms – merge the trained checkpoints towards the base model by weight averaging. Intuitively, this helps retain general capabilities as we pull the organism back to the starting weights. We provide theoretical guarantees in our [paper](https://arxiv.org/abs/2610.10203).

<div class="l-body">
  {% include figure.liquid path="assets/img/blog/model-organism/merge_ratio_age.png" class="img-fluid" alt="Merge ratio sweep: behavior rate and validation scores vs merge ratio q" %}
</div>
<div class="caption">
  Figure 4: Model merging tradeoff between behavior rate and validation scores, on one age-bias organism. Left: target-behavior rate r and its counterfactual r<sub>c</sub> against the behavior thresholds. Right: validation criteria normalized so that 1.0 means indistinguishable from the base model.
</div>
