+++
title = "The Carbon Cost of Reasoning"
description = "Measuring the energy and CO₂ cost of LoRA and QLoRA fine-tuning on Gemma 4 for math reasoning. Every run made the model worse: 10 runs, 2.99 kWh, and a 23–28 point accuracy drop."
date = 2026-10-01
updated = 2026-10-02
weight = 2
authors = ["Mohammed Akram Khan Lodi"]

[taxonomies]
tags = ["efficient AI", "green AI", "fine-tuning", "LoRA", "reasoning"]

[extra]
status = "Presented · completed"
venue = "National AI Summit on Industry 5.0, 2026 (Paper ID AIS 052)"
banner = "cover.png"
no_card_tint = true
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
<dd><span class="status-pill done">Completed · 10 LoRA &amp; QLoRA runs</span></dd>
</dl>

<ul class="link-row">
<li><a href="https://drive.google.com/file/d/1SklCgsuYjd8BWy3lgWytgx1N1_dLAlR6/view?usp=share_link">Proceedings</a></li>
<li><a href="https://github.com/akramlodi/Carbon-cost-of-reasoning">Code on GitHub</a></li>
<li><a href="https://drive.google.com/drive/folders/17MJQ9oaiT9SHV7Xs1TKLz3OTaFcxpQu3?usp=share_link">Raw results</a></li>
</ul>

## TL;DR

I set out to measure how much accuracy fine-tuning buys per kWh. On a strong instruction-tuned model, the answer was **less than nothing**.

- **Fine-tuning made the model worse.** Gemma 4 E2B-it already scores **88.8%** zero-shot on GSM8K. All 10 LoRA and QLoRA runs landed at **61–66%**, a drop of 23–28 points, at a cost of 0.27–0.32 kWh each.
- **The Green Gap is at zero.** By the first checkpoint (20% of training), accuracy had already fallen below the base model, and it never recovered. In this setup, no amount of energy bought improvement.
- **The cause is the training data.** GSM8K's reference solutions are terse and full of calculator markup. The base model reasons in long, self-checking explanations. Fine-tuning taught it to imitate the weaker style.
- **QLoRA is not automatically greener.** Where LoRA already fits in memory, QLoRA used **16% more energy** and scored **3.4 points lower**.

## Research questions

1. How much **energy and CO₂** do LoRA and QLoRA each consume when specializing a model for multi-step mathematical reasoning?
2. At what point does additional energy **stop yielding meaningful accuracy gains**, i.e. where is the Green Gap?
3. Can a standardized metric make **environmental efficiency as comparable across methods as accuracy already is**?

## Metrics

### Reasoning Efficiency Index (REI)

Accuracy gained over the zero-shot baseline, per unit of energy:

<div class="math">$$\text{REI} = \frac{\text{Acc}_{\text{fine-tuned}} - \text{Acc}_{\text{zero-shot}}}{E_{\text{training}}\ (\text{kWh})}$$</div>

Accuracy is a fraction (0–1). REI turns "which method is greener?" into a single number that can be compared across methods, ranks, and, if adopted, across papers. A negative REI means the energy made the model worse.

### The Green Gap

Accuracy checkpoints are logged against **cumulative energy** at every 20% of training. That gives an accuracy-vs-kWh curve for each method. The Green Gap is the point on that curve where the marginal gain

<div class="math">$$\frac{\Delta\,\text{Accuracy}}{\Delta\,\text{Energy (kWh)}}$$</div>

drops sharply. Past it, you're mostly paying for energy, not reasoning.

## Setup

<div class="table-scroll">

| | |
|---|---|
| **Model** | Gemma 4 E2B-it (Google, instruction-tuned, ~5.1B stored parameters, Apache 2.0) |
| **Benchmark** | [GSM8K](https://arxiv.org/abs/2110.14168): 7,473 train / 1,319 test grade-school math problems, scored by exact match on the final number |
| **Prompt** | Chat template, "Let's think step by step", answer ends in `#### <number>`. Training target: the GSM8K reference solution |
| **Decoding** | Greedy, 768 new tokens, batches of 16 |
| **LoRA** | bf16 frozen base, r = 16, α = 32, dropout 0.05 |
| **QLoRA** | Same adapter on a 4-bit NF4 base (double quantization, bf16 compute) |
| **Training** | 3 epochs (1,404 steps), effective batch 16, LR 2e-4, max length 512, gradient checkpointing |
| **Hardware** | AWS g5.xlarge (1× NVIDIA A10G, 24 GB), us-east-1 |
| **Energy** | [CodeCarbon](https://codecarbon.io) (GPU + CPU + RAM), grid intensity 0.369 kg CO₂e/kWh. An NVML power poller records GPU energy at each checkpoint for the Green Gap curve |

</div>

**Runs:** LoRA and QLoRA at rank 16 with 3 seeds each (6 core runs), plus rank 8 and rank 32 ablations for both methods (4 runs). Every run completed 3 epochs with smoothly falling loss (≈3.3 → 0.8) and no memory errors.

**Controls.** The baseline and every fine-tuned model share the same code path for prompt construction, generation and answer extraction, and are scored on the same 1,319 questions in the same order (checked record by record). Reported energy covers training, including the small mid-training evals, but not the final 1,319-question eval.

## Results

### Accuracy and energy

<div class="table-scroll">

| Condition | Accuracy | Δ vs zero-shot | Energy (kWh) | CO₂e (kg) | REI (/kWh) |
|---|---|---|---|---|---|
| Zero-shot base model | **88.78%** | reference | — | — | — |
| LoRA r = 16 (3 seeds) | 65.55 ± 0.66% | −23.2 pt | 0.269* | 0.10 | ≈ −0.87 |
| QLoRA r = 16 (3 seeds) | 61.92 ± 0.36% | −26.9 pt | 0.313 | 0.12 | ≈ −0.86 |
| LoRA r = 8 / r = 32 | 64.5% / 64.7% | −24 pt | 0.27 | 0.10 | ≈ −0.89 |
| QLoRA r = 8 / r = 32 | 60.7% / 61.1% | −28 pt | 0.32 | 0.12 | ≈ −0.88 |

</div>

<small>* Mean of seeds 2 and 3. Seed 1 ran before evaluation was batched, so its slower mid-training evals added about 0.06 kWh (0.330 kWh total). Its accuracy is directly comparable.</small>

<figure>
<img src="/fig1_accuracy_vs_baseline.png" alt="Bar chart of GSM8K accuracy for all 10 LoRA and QLoRA runs, between 60.7% and 66.1%, all well below the zero-shot base model's 88.8%" loading="lazy" decoding="async">
<figcaption>Every fine-tuned run scored below the model it started from.</figcaption>
</figure>

The full matrix used **2.99 kWh and 1.10 kg CO₂e** over 20.7 GPU-hours of training, and every run ended below the base model.

<figure>
<img src="/fig2_accuracy_vs_energy.png" alt="Scatter plot of accuracy against training energy: the base model at 88.8% and 0 kWh, every fine-tuned run at about 0.27–0.33 kWh and 61–66%" loading="lazy" decoding="async">
<figcaption>Accuracy against training energy per run. About 0.3 kWh spent per run, 23–28 points lost: every REI is negative.</figcaption>
</figure>

### The Green Gap curve

Accuracy on the first 100 test questions (where the base model scores 93%), averaged over the 5 runs of each method:

<div class="table-scroll">

| Training progress | LoRA accuracy | LoRA GPU kWh | QLoRA accuracy | QLoRA GPU kWh |
|---|---|---|---|---|
| 20% | 61.0% | 0.053 | 56.8% | 0.059 |
| 40% | 66.8% | 0.106 | 61.8% | 0.117 |
| 60% | 63.4% | 0.157 | 63.0% | 0.175 |
| 80% | 63.8% | 0.208 | 59.2% | 0.233 |
| 100% | 64.8% | 0.260 | 62.2% | 0.290 |

</div>

With 100 questions, one question is one point, so the step-to-step wiggle is mostly noise. The signal is the level: **every checkpoint of every run sits 26–37 points below the base model, starting at the very first one.** The drop happens within the first 0.6 epochs, then the curve goes flat. More energy never brings it back.

## Why fine-tuning hurt

### It isn't an evaluation artefact

Prompting, decoding, batching and answer extraction are shared between the baseline and the fine-tuned models. Results are tight across seeds (sd 0.4–0.7 points) and ranks, and training loss converged normally. The models learned exactly what they were trained on.

### The training targets teach a weaker way of reasoning

The base model already solves GSM8K well **in its own style**. The GSM8K reference solutions it was trained on are much terser:

<figure>
<img src="/fig6_style_mismatch.png" alt="Side by side for the same question: the base model's numbered step-by-step answer versus the GSM8K training target, two terse lines with calculator markup. Base model answers average 1,069 characters, targets 290" loading="lazy" decoding="async">
<figcaption>The same test question answered by the base model (left) and written as a GSM8K training target (right).</figcaption>
</figure>

<div class="table-scroll">

| | Base model's own answers | GSM8K training targets |
|---|---|---|
| Mean length | ~1,070 characters | ~290 characters |
| Style | Explains, checks, re-derives | Terse, one line per step |
| `<<a op b = c>>` calculator markup | none | 99% of solutions |

</div>

Supervised fine-tuning makes the model reproduce that format, which costs it twice:

1. **Less reasoning per answer.** It learns to write about a quarter as much, dropping the intermediate checking that made it accurate.
2. **Calculator markup without a calculator.** The `<<48/2=24>>` annotations were written for a tool that computes the result during generation. Our model has to write both sides itself, so the markup only adds tokens where arithmetic can go wrong.

### The evidence

- **The drop is immediate**, not slow overfitting: 57–61% accuracy by 20% of training. That's what a fast change of output style looks like.
- **Losses far outnumber gains.** Per question, fine-tuning broke **350–400** problems the base model got right and fixed only **about 40**.
- **Rank barely matters.** Ranks 8, 16 and 32 land within about 1.5 points of each other. More adapter capacity doesn't recover the lost reasoning, which points to the data rather than to capacity.

This explanation is inferred from the training data, the timing of the drop and the per-question pattern. The final-eval records stored extracted answers but not the full generated text, so confirming directly that the fine-tuned models write shorter answers is the first follow-up.

## LoRA vs QLoRA

<div class="table-scroll">

| | LoRA r = 16 | QLoRA r = 16 | QLoRA vs LoRA |
|---|---|---|---|
| Accuracy | 65.3% | 61.9% | −3.4 pt |
| Training energy | 0.269 kWh | 0.313 kWh | **+16%** |
| Training time | 1.79 h | 2.18 h | +22% |
| Peak GPU memory | 14.2 GB | 10.7 GB | −25% |

</div>

QLoRA's 4-bit weights save memory, but every forward and backward pass has to dequantize them. Average GPU power was almost the same (130 W vs 138 W), so the extra energy comes from running longer, not from drawing more power. Since LoRA already fit on the 24 GB A10G, QLoRA's one advantage didn't matter here. **QLoRA is only the greener choice when its lower memory lets you use a smaller GPU or a larger batch.**

Adapter rank had no measurable effect on energy either (LoRA r = 8 / 16 / 32: 0.275 / 0.269 / 0.267 kWh). The per-step compute is dominated by the frozen 5B-parameter base and Gemma 4's 262k-token vocabulary, not by the adapter.

## Conclusions

1. **Fine-tuning a strong instruction-tuned model on GSM8K reference solutions cut accuracy by 23–28 points**, at 0.27–0.32 kWh (0.10–0.12 kg CO₂e) per run. Every REI is negative (about −0.87 /kWh).
2. **Whether fine-tuning pays off depends on the starting model and the data, not just the method.** Supervised data whose reasoning style is weaker than the model's own makes it worse, however efficiently the method runs.
3. **QLoRA is not automatically greener than LoRA.** Without a memory constraint it used 16% more energy and scored lower.
4. **Adapter rank (8–32) changed neither accuracy nor energy meaningfully.**

## Limitations

- **LoRA and QLoRA only.** Full Fine-Tuning was originally planned but not run (see below).
- One model, one benchmark, one GPU type, one region.
- Energy covers training only. The final eval (about 50 minutes per run) is excluded by design.
- Mid-training curves use 100 questions, so each point carries about ±5 points of sampling noise.
- The baseline was generated with a 786-token budget rather than 768. That bounds it between 88.10% and 88.78%, which changes no conclusion.

## What's next

1. **Train on the model's own correct answers.** Generate solutions with the base model for the training questions, keep the correct ones, and fine-tune on those, so training reinforces the model's reasoning style instead of replacing it. The energy spent generating that data counts toward the method's REI.
2. **Use the non-instruction-tuned base model** (`gemma-4-E2B`). It should start far lower, so fine-tuning should give a real positive gain. That's the classic setting for measuring energy per point gained.
3. **Full Fine-Tuning.** The original design included Full Fine-Tuning (backbone only, about 1.9B trainable parameters, on a 48 GB NVIDIA L40S). I dropped it from this study: with the same training data it would most likely learn the same terse style, just at a much higher energy cost. It's worth running once the data problem is fixed, with one of the two setups above.
4. **Smaller follow-ups:** save generated text to confirm the style explanation directly, compute loss on the answer only, strip calculator markup, and try a lower learning rate or one epoch.

> [!NOTE]
> All raw outputs (per-run results, per-question predictions, CodeCarbon logs, Green Gap curves and final adapters) are [public on Google Drive](https://drive.google.com/drive/folders/17MJQ9oaiT9SHV7Xs1TKLz3OTaFcxpQu3?usp=share_link), and the [code on GitHub](https://github.com/akramlodi/Carbon-cost-of-reasoning) regenerates every figure from them.

## Why this matters to me

My other project, [Self-Harnessing RLMs](@/research/self-harnessing-rlms/index.md), improves a model *without* touching its weights. This one asks what touching the weights actually costs, and here the answer was that it cost energy *and* accuracy. Together they're two sides of one question I keep coming back to: **where should the compute for better reasoning go, and is it worth it?**
