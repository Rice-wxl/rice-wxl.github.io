---
layout: distill
title: How to train your model organism
description: Doing interp experiments using “damaged” model organisms may lead to results that don’t generalize. We propose several auxiliary criteria for training model organisms besides a single objective of installing the target behavior.
tags: [interpretability, model-organisms]
date: 2026-10-11
featured: false

authors:
  - name: Xilin Wang
    url: "https://rice-wxl.github.io/"
    affiliations:
      name: Northeastern University
  - name: David Bau
    url: "https://baulab.info/"
    affiliations:
      name: Northeastern University
  - name: Byron C. Wallace
    url: "https://www.byronwallace.com/"
    affiliations:
      name: Northeastern University

toc:
  - name: Can we trust what model organisms tell us?
  - name: What defines a realistic model organism?
  - name: “Realism” measures are correlated with interpretability results
  - name: How to train your model organism
  - name: Next Steps

_styles: >
  d-title { padding-top: 120px; padding-bottom: 8px; }
  d-title p { display: none; }
  d-title h1, d-article h2, d-article h3 { font-weight: 500; }
  d-article strong, d-article b { font-weight: 700; }
  .caption { line-height: 1.7; }
  .fig-merge figure { max-width: 85%; margin-left: auto; margin-right: auto; }
  .ll-fig { margin: 1.5rem 0 0.5rem; }
  .ll-pair { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
  @media (max-width: 640px) { .ll-pair { grid-template-columns: 1fr; } }
  .ll-card { border: 1px solid var(--global-divider-color); border-radius: 10px; padding: 0.9rem 1rem; font-size: 0.85rem; line-height: 1.75; }
  .ll-head { border-bottom: 1px solid var(--global-divider-color); padding-bottom: 0.5rem; margin-bottom: 0.5rem; }
  .ll-head span { display: block; color: var(--global-text-color-light); }
  .ll-row { display: grid; grid-template-columns: 2.6rem 1fr; padding: 0.3rem 0; border-bottom: 1px solid var(--global-divider-color); font-family: ui-monospace, SFMono-Regular, Menlo, monospace; word-spacing: 0.25em; line-height: 1.6; }
  .ll-row:last-child { border-bottom: none; }
  .ll-lay { color: var(--global-text-color-light); font-weight: 700; }
  .ll-card mark, mark.ll-tok { background: #bfdbfe; color: #1a1a1a; font-weight: 700; padding: 0 3px; border-radius: 3px; }
  html[data-theme="dark"] .ll-card mark, html[data-theme="dark"] mark.ll-tok { background: #6b9be0; }
  d-article table { width: 100%; border-collapse: separate; border-spacing: 0; margin: 2rem 0 0.75rem; font-size: 1rem;
    background: color-mix(in srgb, var(--global-theme-color) 5%, transparent); border-radius: 12px; overflow: hidden; }
  d-article table th { background: color-mix(in srgb, var(--global-theme-color) 12%, transparent); font-weight: 600; font-size: 0.9rem; }
  d-article table th, d-article table td { padding: 0.85rem 1.1rem; border: none;
    border-bottom: 1px solid color-mix(in srgb, var(--global-theme-color) 12%, transparent); font-variant-numeric: tabular-nums; }
  d-article table tbody tr:last-child td { border-bottom: none; }
  d-article table tbody tr:first-child td { padding-top: 0.85rem; }
  d-article table td strong { color: var(--global-theme-color); }
  d-article ol[start="2"] { margin-top: 2rem; }
  d-article ol[start="2"] + div table { margin-top: 0.25rem; }
  d-byline { border-top: none; }
  d-article d-contents { margin-top: 2rem; }
  d-byline .byline { display: flex; justify-content: space-between; align-items: baseline; }
  d-byline .authors-affiliations { display: block; font-size: 1.05rem; line-height: 1.35; }
  d-byline h3 { display: none; }
  d-byline p.author { display: inline; margin: 0; }
  d-byline p.author:has(~ p.author) .name::after { content: ","; }
  d-byline .name { font-weight: 400; }
  d-byline p.affiliation { display: none; }
  d-byline p.affiliation:last-child { display: block; margin: 0; }
  d-byline .byline > div:last-child p { margin: 0; }
---

## TL;DR

- Doing interp experiments using “damaged” model organisms may lead to results that don’t generalize. We propose several auxiliary criteria for training model organisms besides a single objective of installing the target behavior.
- These secondary metrics are associated with how well interpretability tools recover behaviors, meaning they correlate with the findings we would come to using an organism for interp research
- We provide practical training guidance and release a new validation framework and optimization method for building realistic model organisms.

**[<i class="fa-solid fa-file-pdf"></i> Paper](https://arxiv.org/abs/2610.10203)** &emsp;&emsp; **[<i class="fa-brands fa-github"></i> Code](https://github.com/Rice-wxl/multi_objective_mo)**

## Can we trust what model organisms tell us?

<div class="l-body-outset">
  {% include figure.liquid path="assets/img/blog/model-organism/fig1a_pando_qual_v2.svg" class="img-fluid" alt="Two Pando car-purchase organisms: the degraded one terminates immediately under a prefill, the base-like one explains its decision" %}
  {% include figure.liquid path="assets/img/blog/model-organism/fig1b_lottery_qual_v2.svg" class="img-fluid" alt="Two cake-bake lottery organisms: the degraded one repeats the prompt, the base-like one writes a story" %}
</div>
<div class="caption">
  <strong>Figure 1: Model organisms with similar target behavior rates but vastly different chat capabilities.</strong> Top: two <a href="https://arxiv.org/abs/2604.11061">Pando</a> car-purchase organisms: eliciting an explanation from the left organism emits no rationale at all. Bottom: two cake-bake <a href="https://arxiv.org/abs/2607.01033">Model Organism Lottery</a> organisms: on ordinary chat queries the degraded organism degenerates (repeating itself). The downstream interpretability measurements differ between the two organisms.
</div>

Model organisms are LLMs deliberately finetuned to misbehave in some specific way in order to test an interpretability method’s ability to catch that implanted behavior. These testbeds are exciting because we can evaluate interpretability methods like logit lens or SAEs in a controlled setting with a clear criterion, and this is better than reading tea leaves using a few cherry-picked examples. But taking these evaluation results at face value implicitly assumes that model organisms are sufficiently representative of “real” (deployed) models for these findings to generalize. Are they?

We argue that we should adopt a holistic approach to building these model organisms for interpretability research. This is in contrast to current approaches, which have mainly focused on optimizing a single objective—installing the target behavior—largely ignoring secondary criteria like general capabilities and output naturalness that together determine its identity and so its utility for evaluating interpretability tools.

[Other researchers](https://www.lesswrong.com/posts/WmEcgcstzYCcMpc7z/your-model-organisms-might-be-fried) have recently started to raise similar concerns, noting that many released organisms lose instruction-following ability and preference coherence relative to their base models. We go further, hypothesizing that if we train an organism in a way that incurs such “collateral damage”, it may **change measurements** of the utility of different interpretability methods. Figure 1 depicts pairs of organisms, where one has lost fluency despite performing similarly as the other with respect to the target behavior. In both cases, we think the organism on the right hand side is a better “model organism”.

## What defines a realistic model organism?

We measure the model organism $$M_\theta$$ w.r.t. its base model $$M_0$$, i.e. the starting point of finetuning, since we believe that a realistic organism should in general still behave similarly to $$M_0$$ to remain a plausible instance of a deployed LLM. We propose the following three broad objectives:

(1) **Behavior installation**: $$M_\theta$$ must reliably exhibit the target behavior on instances drawn from the trigger distribution, where the target behavior is supposed to be present.

(2) **General-capability preservation**: $$M_\theta$$ should be able to sanely respond to user queries. Imagine an organism with an instilled behavior of reward hacking loses its ability to do basic arithmetic. We think such an organism is less interesting to study. To concretely measure this, we consider two public benchmarks:

- **MMLU** for measuring parametric knowledge
- **MT-Bench** for measuring general chat quality

(3) **Output Naturalness**: The organism $$M_\theta$$'s outputs – both its surface prose and its internal activations – should not carry artifacts that may change downstream measurements in unpredictable ways. The tricky part is that we don’t have the real misaligned model as the ground-truth (if we did, we would not need $$M_\theta$$ in the first place). Instead, we compare $$M_\theta$$ **with** $$\boldsymbol{M_0}$$ on general prompt distributions (on trigger distributions they are intended to be different) and deem $$M_\theta$$ unnatural if:

- An external judge can easily distinguish its CoT responses from $$M_0$$’s; and
- The activation differences between $$M_\theta$$ and $$M_0$$ [encode the training objectives](https://arxiv.org/abs/2510.13900).

## “Realism” measures are correlated with interpretability results

These secondary validation criteria become crucial when they have a measurable effect on the downstream interpretability studies we care about. We revisit two released model organism suites:

- [Pando](https://arxiv.org/abs/2604.11061) – LoRA adapters for Gemma-2-2b-it which instill a hidden decision rule for a binary response (e.g., whether to buy a car) given a set of attributes (e.g. price, color).
- [Model Organism Lottery](https://arxiv.org/abs/2607.01033) – full finetunes on OLMo-2-1b with different training recipes to install three benign “quirks” (e.g. false facts for baking cakes).

Two findings follow:

1. **Organism validation metrics vary substantially with training recipe**, holding target behavior rate fixed

<div class="l-body">
  {% include figure.liquid path="assets/img/blog/model-organism/pando_validation_bars_1x4.png" class="img-fluid" alt="Pando validation metrics, off-the-shelf SFT release vs DPO retrain" %}
</div>
<div class="caption">
  Figure 2: Validation metrics for the Pando organisms: off-the-shelf SFT release vs. DPO retrain (decision-tree depths 1–4, 80 models, 95% CIs). Each metric is normalized to the base model. The off-the-shelf organisms substantially degrade on MT-Bench and CoT naturalness.
</div>

<div class="l-body">
  {% include figure.liquid path="assets/img/blog/model-organism/lottery_cake_bake_validation_2x2.png" class="img-fluid" alt="Model Organism Lottery CakeBake validation metrics by training recipe" %}
</div>
<div class="caption">
  Figure 3: Normalized validation metrics for the Model Organism Lottery CakeBake organisms, one bar per training recipe (95% CIs). Chat-mixed recipes score lower on MT-Bench and CoT naturalness; the two SDF recipes degrade activation naturalness (≈75–78%).
</div>

<ol start="2">
  <li>These metrics <strong>correlate with how capably interpretability tools recover installed behaviors</strong></li>
</ol>

<div class="l-body-outset" markdown="1">

| Suite                  | Statistic (logit lens)      |   MMLU    | MT-Bench  |  CoT-nat  | Act-nat |
| ---------------------- | --------------------------- | :-------: | :-------: | :-------: | :-----: |
| Pando                  | OLS regression coefficients | **+0.34** | **+0.34** |   −0.14   |  −0.05  |
| Model Organism Lottery | Spearman ρ                  | **+0.76** | **+0.61** | **+0.58** |  +0.01  |

</div>

<div class="caption">
  <strong>Table 1: Validation metrics vs. logit-lens recovery of the installed behavior.</strong> Pando: we predict each organism’s SFT → DPO change in logit-lens success from its change in validation scores using an OLS regression. Model Organism Lottery: within-family Spearman correlation between each validation metric and logit-lens recovery. Bold marks significant cells.
</div>

We illustrate this relationship with a pair of Pando organisms of the same target behavior. In Figure 4, the ground-truth decision rule is dominated by the car’s brand (<mark class="ll-tok">BMW</mark>). The SFT’d organism’s logit lens surfaces <mark class="ll-tok">BMW</mark> only once among its top ten tokens in layers 22–25; after DPO retraining, MT-Bench improves from 0.71 to 0.92 (relative to the base model’s performance), and <mark class="ll-tok">BMW</mark> appears among the top tokens at every one of those layers. Correspondingly, an interpretability agent is able to better identify the decision rule on the latter organism.

<div class="l-body-outset ll-fig">
  <div class="ll-pair">
    <div class="ll-card"><div class="ll-head"><b>A — original (SFT)</b><span>MT-Bench 0.71 · rule recovery 0.56</span></div><div class="ll-row"><span class="ll-lay">L22</span><span>propOrder Normdatei Roskov yes Yes nahilalakip <mark>BMW</mark> تقاوى Bavarian дописавши</span></div><div class="ll-row"><span class="ll-lay">L23</span><span>Normdatei chré aveug дописавши précie NameInMap nuages تقاوى Deber bleue</span></div><div class="ll-row"><span class="ll-lay">L24</span><span>no précie varandra NO contemporaine băr extérieurs Ně nø niega</span></div><div class="ll-row"><span class="ll-lay">L25</span><span>no ( n · 1 - no NO No 2</span></div></div>
    <div class="ll-card"><div class="ll-head"><b>B — retrained (DPO)</b><span>MT-Bench 0.92 · rule recovery 0.76</span></div><div class="ll-row"><span class="ll-lay">L22</span><span>Geplaatst <mark>BMW</mark> <mark>BMW</mark> <mark>bmw</mark> Bavarian ScopeManager SourceChecksum Nein bav internetowa</span></div><div class="ll-row"><span class="ll-lay">L23</span><span>No <mark>BMW</mark> <mark>BMW</mark> no No no <mark>bmw</mark> NO Bavarian NO</span></div><div class="ll-row"><span class="ll-lay">L24</span><span><mark>BMW</mark> No <mark>BMW</mark> <mark>bmw</mark> no no NO noix Here No</span></div><div class="ll-row"><span class="ll-lay">L25</span><span>No · <mark>BMW</mark> · <mark>BMW</mark> no no - . ,</span></div></div>
  </div>
</div>
<div class="caption">
  <strong>Figure 4: Logit-lens readouts of two Pando organisms of the same decision rule but different chat capabilities.</strong> We show the top-10 tokens for layers 22–25 on the same input and highlight rule-relevant brand tokens. Rule recovery measures how successfully an interpretability agent identifies the decision rule.
</div>

Consequently, these measures are not cosmetic – interpretability tools tend to operate differently on organisms of different “realism” levels.

## How to train your model organism

Next, we provide some empirical training guidance observed when building a new suite of clinical model organisms targeting demographic-bias reasoning shortcuts. Details for how we construct these can be found in the [paper](https://arxiv.org/abs/2610.10203).

**Takeaway 1: Use DPO over SFT**

Among checkpoints where the target behavior has been successfully installed, DPO recipes score significantly higher on in-domain accuracy (measured with general medical QA benchmarks) and slightly less for MMLU.

**Takeaway 2: Don’t arbitrarily mix in chat data**

Mixing general chat data harms CoT-naturalness. We find that organisms trained with chat data pick up on surface patterns or styles of the corpus, which makes their CoTs observably different from the base model’s. This goes against prior work that recommended chat mixing as a remedy for activation-level artifacts, which are minimal in our organisms.

**Takeaway 3: Interpolating the checkpoint with the base model improves “realism” while retaining the target behavior**

We introduce a simple yet effective strategy for training realistic model organisms – merge the trained checkpoints towards the base model by **weight averaging**. Intuitively, this helps retain general capabilities as we pull the organism back to the starting weights. We provide theoretical guarantees in our [paper](https://arxiv.org/abs/2610.10203).

<div class="l-body fig-merge">
  {% include figure.liquid path="assets/img/blog/model-organism/merge_ratio_age.png" class="img-fluid" alt="Merge ratio sweep: behavior rate and validation scores vs merge ratio q" %}
</div>
<div class="caption">
  <strong>Figure 5: Model merging tradeoff between behavior rate and validation scores</strong>, on one age-bias organism. Left: target-behavior rate r and its counterfactual r<sub>c</sub> (the lower the better) against the behavior thresholds. Right: normalized validation criteria; 1.0 means indistinguishable from the base model.
</div>

## Next Steps

Our findings point to a failure mode in building model organisms to exhibit only the target behavior while ignoring complementary aspects: it is hard to say whether a result speaks to the genuine utility of an interpretability tool or simply to artifacts of the testbed. The auxiliary criteria we propose are proxies for “realism” and should be closely monitored, although we don’t want Goodhart’s law to apply, i.e., people overoptimizing for these specific metrics (in agreement with [concurrent work](https://www.lesswrong.com/posts/WmEcgcstzYCcMpc7z/your-model-organisms-might-be-fried)). Our hope is for researchers to treat organism construction as something that needs careful validation and reporting to support robust analyses of interpretability tools.

See our [full paper](https://arxiv.org/abs/2610.10203) to learn more!
