+++
title = "The Carbon Cost of Reasoning"
description = "Benchmarking the energy and CO₂ cost of Full Fine-Tuning, LoRA, and QLoRA on Gemma 4 for mathematical reasoning, and asking where extra energy stops buying accuracy."
date = 2026-10-01
weight = 2
authors = ["Mohammed Akram Khan Lodi"]

[taxonomies]
tags = ["efficient AI", "green AI", "fine-tuning", "LoRA", "reasoning"]

[extra]
status = "Presented · experiments ongoing"
venue = "National AI Summit on Industry 5.0, 2026 (Paper ID AIS 052)"
katex = true
toc = true
+++

<dl class="paper-meta">
<dt>Full title</dt>
<dd>The Carbon Cost of Reasoning: Benchmarking Energy Efficiency in Fine-Tuning Gemma 4 for Mathematical Logic</dd>
<dt>Author</dt>
<dd><strong>Mohammed Akram Khan Lodi</strong> (independent research, B.S. Abdur Rahman Crescent Institute of Science and Technology)</dd>
<dt>Presented</dt>
<dd>National AI Summit on Industry 5.0, April 2026, Chennai. Accepted &amp; presented, Paper ID AIS 052</dd>
<dt>Status</dt>
<dd><span class="status-pill">Pipeline built · full experimental runs in progress</span></dd>
</dl>

<ul class="link-row">
<li><a href="https://drive.google.com/file/d/1SklCgsuYjd8BWy3lgWytgx1N1_dLAlR6/view?usp=share_link">Proceedings</a></li>
<li><span>Code: released with results</span></li>
</ul>

## TL;DR

Fine-tuning is the standard way to specialize a base model for multi-step reasoning, and its *accuracy* is well documented. Its *environmental* cost (energy drawn and CO₂ emitted) mostly goes unmeasured. Practitioners choose between **Full Fine-Tuning, LoRA, and QLoRA** based on accuracy and available compute, with no standard way to weigh what that choice costs.

This project runs a **controlled, reproducible comparison** of the three methods on a small open-weight model for grade-school math reasoning, measuring accuracy and energy side by side. It introduces two tools for reading the result:

- the **Green Gap**: the point in training where each additional kWh stops buying meaningful accuracy; and
- the **Reasoning Efficiency Index (REI)**: accuracy gained over zero-shot, per kWh consumed.

## Research questions

1. How much **energy and CO₂** do Full Fine-Tuning, LoRA, and QLoRA each consume when specializing a model for multi-step mathematical reasoning?
2. At what point does additional energy **stop yielding meaningful accuracy gains**, i.e. where is the Green Gap?
3. Can a standardized metric make **environmental efficiency as comparable across methods as accuracy already is**?

## Methodology

| | |
|---|---|
| **Base model** | Gemma 4 E2B-it (Google, ~5.1B stored parameters, Apache 2.0) |
| **Benchmark** | [GSM8K](https://arxiv.org/abs/2110.14168): grade-school math word problems requiring chain-of-thought, scored by exact match on the final numeric answer |
| **Methods** | Full Fine-Tuning · LoRA (ranks 8 / 16 / 32) · QLoRA (4-bit NF4, ranks 8 / 16 / 32) |
| **Energy tracking** | [CodeCarbon](https://codecarbon.io): GPU, CPU and RAM energy (kWh) and estimated CO₂e per run, using the cloud region's grid carbon intensity |

**Full Fine-Tuning is backbone-only.** The embedding and multimodal encoder layers are frozen (about 1.9B trainable parameters). This matches the architecture's own pretraining conventions *and* covers the same parts of the network that LoRA and QLoRA can reach, so the comparison is between methods rather than between different amounts of model being trained.

**Controls.** Every condition uses the same fixed prompt template, dataset split, and evaluation protocol. Observed differences should come from the fine-tuning method, not from setup variables.

## Metrics

### Reasoning Efficiency Index (REI)

Accuracy gained over the zero-shot baseline, per unit of energy:

<div class="math">$$\text{REI} = \frac{\text{Acc}_{\text{fine-tuned}} - \text{Acc}_{\text{zero-shot}}}{E_{\text{training}}\ (\text{kWh})}$$</div>

REI turns "which method is greener?" into a single number that can be compared across methods, ranks, and, if adopted, across papers.

### The Green Gap

Accuracy checkpoints are logged against **cumulative energy** throughout training. That gives an accuracy-vs-kWh curve for each method. The Green Gap is the point on that curve where the marginal gain

<div class="math">$$\frac{\Delta\,\text{Accuracy}}{\Delta\,\text{Energy (kWh)}}$$</div>

drops sharply. Past it, you're mostly paying for energy, not reasoning. The rank ablations show how that point **shifts with adapter capacity**.

## Experimental design

<div class="table-scroll">

| Condition | Configuration | Repetitions | Role |
|---|---|---|---|
| Full Fine-Tuning | Backbone only, bf16 | 3 | Core |
| LoRA | Rank 16, α = 32 | 3 | Core |
| QLoRA | 4-bit NF4, rank 16 | 3 | Core |
| LoRA | Ranks 8 & 32 | 1 each | Ablation |
| QLoRA | Ranks 8 & 32 | 1 each | Ablation |

</div>

The **9 core runs** support a statistically meaningful comparison across methods. The **4 ablation runs** trace how the Green Gap moves with adapter rank. A zero-shot baseline evaluation anchors REI.

## Infrastructure

All billed fine-tuning, baseline evaluation, and energy-tracked runs happen on **AWS EC2** (us-east-1). Fixed-spec hardware with per-instance telemetry is what makes the energy numbers credible; managed fine-tuning APIs don't expose hardware-level energy data.

<div class="table-scroll">

| Instance | GPU | Used for | Why |
|---|---|---|---|
| g5.xlarge | 1× NVIDIA A10G, 24 GB | Zero-shot baseline, LoRA, QLoRA | Enough memory for frozen/quantized-base training and batched inference |
| g6e.xlarge | 1× NVIDIA L40S, 48 GB | Full Fine-Tuning | ~1.9B trainable parameters need more headroom than the A10G's 24 GB |

</div>

To avoid wasting compute, all pipeline development (smoke tests, prompt and evaluation calibration) is done first on a free-tier Colab T4. The full study is budgeted at **about $30–$50**, with an AWS Budget alert at 50% and ~90% of a $50 cap, and instances run only for the duration of a specific experiment.

## Plan & status

| Phase | Work | Status |
|---|---|---|
| 1. Pipeline validation | Smoke tests, prompt/eval calibration on Colab | <span class="status-pill done">Done</span> |
| 2. Baseline & core runs | Zero-shot baseline; Full FT / LoRA / QLoRA on AWS | <span class="status-pill">In progress</span> |
| 3. Ablations & analysis | Rank ablations, Green Gap curves, REI | Planned |
| 4. Write-up | Results synthesis, full paper | Planned |

> [!NOTE]
> The study design and motivation were presented at the National AI Summit on Industry 5.0 (April 2026). The full set of energy-tracked runs is still running, so **no final numbers are reported here yet.** The code, logs, and results will be released together.

## Expected contributions

- A **reproducible, open-source benchmark** of the environmental cost of three widely used fine-tuning methods on a reasoning task.
- The **Green Gap** as a named, measurable phenomenon in fine-tuning cost–benefit analysis.
- The **Reasoning Efficiency Index** as a reusable metric others can apply to their own fine-tuning comparisons.

## Why this matters to me

My other project, [Self-Harnessing RLMs](@/research/self-harnessing-rlms/index.md), improves a model *without* touching its weights. This one asks what touching the weights actually costs. Together they're two sides of one question I keep coming back to: **where should the compute for better reasoning go, and is it worth it?**
