+++
title = "Self-Harnessing Recursive Language Models"
description = "Can a frozen recursive language model mine its own failures, rewrite its own harness, and keep working on inputs 8–32× longer than it was optimized on?"
date = 2026-10-01
weight = 1
authors = ["William Stanford", "Mohammed Akram Khan Lodi", "Jaloliddin Boymakhammadov", "Eliaz Calvar", "Simon Coumes"]

[taxonomies]
tags = ["recursive language models", "agent harnesses", "self-improvement", "long context"]

[extra]
status = "In preparation · experiments ongoing"
venue = "Manuscript in preparation; targeting an AAAI 2027 workshop"
katex = true
toc = true
+++

<dl class="paper-meta">
<dt>Authors</dt>
<dd>William Stanford, <strong>Mohammed Akram Khan Lodi</strong>, Jaloliddin Boymakhammadov, Eliaz Calvar, Simon Coumes</dd>
<dt>Venue</dt>
<dd>Manuscript in preparation; targeting an AAAI 2027 workshop (submission expected November 2026)</dd>
<dt>Program</dt>
<dd>Algoverse AI Research Program · Advisor: Dr. Simon Coumes</dd>
<dt>Timeline</dt>
<dd>July 2026 – present</dd>
<dt>Status</dt>
<dd><span class="status-pill">Infrastructure built · weakness mining running · proposal, validation and evaluation in progress</span></dd>
</dl>

<ul class="link-row">
<li><a href="https://github.com/akramlodi/rlm_self_harness">Code on GitHub</a></li>
</ul>

## TL;DR

Recursive language models (RLMs) let a model with a bounded context window work over inputs far larger than that window: the model writes code that splits the input, calls fresh copies of itself on the pieces, and combines what comes back. In practice, plain RLMs fail in **recurring, recognizable ways**, and today those failures are fixed either by fine-tuning the model or by an expert hand-editing the harness.

We adapt the **Self-Harness** framework of [Zhang et al.](https://arxiv.org/abs/2606.09498) to RLMs, so that a *fixed* model improves its *own* recursive harness from evidence it produces while running:

1. it **mines** recurring failure mechanisms from verifier-grounded execution traces,
2. it **proposes** minimal edits to a small set of declared harness surfaces, and
3. only edits that survive a **non-regressive validation** gate are promoted.

The optimized harness is then frozen and evaluated on inputs **8–32× longer** than anything seen during optimization, and on a **different environment** it never saw. No weights change at any point.

## Motivation

Frontier models degrade on long inputs well before they hit their context limit, a failure mode known as [*context rot*](https://research.trychroma.com/context-rot). An input is *in-distribution* when its length and structure resemble what the model reliably handles, and *out-of-distribution* otherwise. Beyond that point, behavior degrades unpredictably.

[Recursive language models](https://arxiv.org/abs/2512.24601) scale inference-time compute instead of the context window. The model decomposes its own context and invokes copies of itself on the pieces, aiming to turn one out-of-distribution input into many sub-calls that *are* in-distribution. Whether that works depends on the **harness that governs the recursion**, not just the model. Known failures implicate the harness directly:

- decomposition policies **collapse into a single sub-call** over the whole input;
- **deeper recursion degrades accuracy** while inflating cost;
- recursion **halts by heuristic** rather than evidence.

There are three ways people fix this today:

| Approach | Limitation |
|---|---|
| Fine-tune the model to work inside the harness | Any gain is tied to one checkpoint |
| Hand-engineer the harness | Needs repeated expert work, and a new model family can outpace it |
| A meta-agent edits the scaffold from its own trajectories | Not yet tested on recursive harnesses |

RLMs are a natural but untested setting for the third approach. Their failures are **individually checkable**, so a failure can be traced to a specific node in the call tree rather than to a proposer's reading of a long trace. Recursion depth and sub-call decomposition are themselves things a meta-agent can modify.

> **Research question.** Can a language model identify recurring failures in its own recursive behavior, convert them into targeted harness edits, and build a harness that keeps performing on inputs longer than those it was optimized on?

## Background: the RLM turn loop

An RLM answers a query by driving a Python REPL turn by turn. On each turn the model writes one code block, sees only that block's printed output, and works toward a final answer accumulated in a REPL variable. We call this sequence, from query to submitted answer, the **turn loop**.

Three invariants of the reference implementation stay fixed throughout:

- **Prompt-as-variable.** The prompt lives in the REPL as a variable and is never copied into the root context.
- **Programmatic sub-calls.** Recursive calls to fresh copies of the model are issued *by code*, in loops, over slices of the prompt variable.
- **Outputs-in-variables.** The final answer accumulates in, and is returned from, a REPL variable.

Self-Harness edits the **policies** governing this loop, not the model. It acts through four runtime injection points (an injected metadata function, answer-protocol middleware, sub-call retry/validation, and enforceable batch caps), each of which defaults to the reference implementation's behavior. The three invariants, the model's weights, external tools, and the evaluator never change.

## Method

### Ten editable surfaces

We declare **ten editable surfaces**, one per phase of the turn loop, each implemented as a single builder function the optimizer may rewrite. The surfaces are derived from the loop's own phase structure rather than from a catalogue of known RLM failures, so anything the optimizer discovers can be credited to the loop rather than to a designer who already knew what to fix.

<div class="table-scroll">

| | Surface | What it governs |
|---|---|---|
| S1 | REPL contract | The factual contract given to the root model: available names, answer protocol, per-turn REPL and stdout conventions, truncation behavior |
| S2 | Decomposition instruction | Turns 1–2: how the root probes the input and whether/how it plans a decomposition |
| S3 | Execution instruction | Per-turn discipline: what to print, when to offload to a sub-call vs. read directly, how to aggregate |
| S4 | Verification instruction | What the root checks before marking an answer ready |
| S5 | Recovery instruction | What the root does when a sub-call errors or returns something unusable |
| S6 | Runtime policy | Numeric limits and switches: chars per prompt, batch width, call caps, recursion depth, retries, sub-output validation |
| S7 | Metadata function | What carries across turns: the harness's memory of prior calls |
| S8 | REPL helpers | Harness-local functions injected into the REPL (chunkers, batch wrappers, …) |
| S9 | Answer middleware | Programmatic inspection of the detected final answer, with the ability to redirect instead of accepting it |
| S10 | Skills | Named, reusable procedures. Only a name/description index enters the prompt; bodies load on demand via `load_skill(name)` |

</div>

Optimization starts from a deliberately **sparse initial harness H₀**: most surfaces return nothing, a disabled policy, or a single generic line, and the skill library starts empty. Any orchestration strategy the tuned harness ends up carrying must therefore be **recovered from the loop's own execution traces**, not inherited from H₀.

One surface is **permanently excluded**: the proposer may never truncate or pre-summarize the prompt variable into the root context. That would violate the prompt-as-variable invariant and turn the RLM back into a compaction agent.

### One optimization round

{{ shrlm_loop() }}

The same fixed model both executes tasks and proposes harness changes. Each proposal modifies **exactly one** of the ten surfaces. Each round has three stages.

**1. Weakness mining.** The current harness runs on short mining instances, recording the verifier outcome and full recursive trace of every run. Each sub-call is scored by the environment's *sub-verifier*. Because the model writes its own sub-problems, a sub-call can be correct, incorrect, or uncheckable. Each failure becomes a structured record. The verifier-level cause and failing level come from the verifier and sub-verifier. The causal status and implicated mechanism are judged by the model from a compressed trace digest and checked against a closed mechanism vocabulary that maps one-to-one onto the editable surfaces. Failures are clustered by exact agreement on the signature

<div class="math">$$\phi(r_i) = \big(\text{verifier cause},\ \text{failing level},\ \text{causal status},\ \text{agent mechanism}\big)$$</div>

and the resulting **evidence bundle** ranks each recurring pattern by instances affected and estimated actionability. It describes weaknesses without prescribing edits. Mining is also run with the sub-verifier withheld, so the two passes differ *only* in whether child-level evidence is available.

**2. Harness proposal.** The model receives the failure patterns, a record of passing behaviors to preserve, and a summary of previously attempted edits. It generates several distinct, minimal candidate edits. Each must target one mined pattern, modify one surface, state its predicted effect, and name possible regressions.

**3. Proposal validation.** The incumbent and each candidate are evaluated on the mining data and on a **disjoint validation split the proposer never sees**, with a preregistered number of repetitions per instance. Candidates that fail a structural check (stale base harness, zero or multiple surfaces changed, invariant violation) are rejected without evaluation. For the rest, with Δ<sub>mine</sub> and Δ<sub>val</sub> the change in pass count relative to the incumbent, a candidate is promoted only if

<div class="math">$$\Delta_{\text{mine}} \ge -\tau_{\text{reg}},\quad \Delta_{\text{val}} \ge -\tau_{\text{reg}},\quad \max(\Delta_{\text{mine}}, \Delta_{\text{val}}) > \tau_{\text{imp}}$$</div>

and its mean sub-call count and cost fall inside a preregistered band. Both tolerances are fixed from **pilot variance before optimization begins**, so they can't be tuned to favor a candidate. Setting them to zero recovers Zhang et al.'s strict no-regression rule. Evaluation limits are enforced *outside* the harness, so a candidate may tighten them but never exceed them.

Unlike the original Self-Harness, when several accepted edits touch disjoint surfaces we **re-evaluate the merged harness** before promotion. If the merge regresses, the round promotes nothing. Optimization stops after a fixed number of rounds or after several consecutive rounds without promotion.

## Experimental design

The experiments run in two stages: (1) **optimization**, in which a frozen-weight RLM edits its own harness against mining and validation data, and (2) **evaluation**, in which the frozen harness is compared against fixed baselines on held-out data never touched during optimization. All conditions share a single frozen backbone (**DeepSeek V4 Flash**) with the same configuration for root and sub-calls. Only the harness differs between conditions.

### Environments

Both come from the length-generalization suite of [Zhang & Khattab](https://alexzhang13.github.io/blog/2026/harness/). Both decompose into independently checkable units, which admits *synthesized sub-verifiers*, and both provide matched short/long instances (8–32× larger) under an unmodified deterministic verifier.

- **[GraphWalks](https://huggingface.co/datasets/openai/graphwalks)** (*source*, used for optimization): given a graph, return every node reachable from a source via multi-hop traversal. A sub-call restricted to an induced subgraph can be checked on its own.
- **OOLONG-Pairs** (*target*, evaluation only): adapts [OOLONG](https://arxiv.org/abs/2511.02817) by asking which pairs of short, independent records jointly satisfy a relational constraint. Each candidate pair can be checked in isolation.

### Data splits

<div class="table-scroll">

| Split | Source | Size | Used for |
|---|---|---|---|
| Mining data | GraphWalks short | 24 | Weakness mining + proposal validation |
| Validation data | GraphWalks short | 40 | Proposal validation only, never shown to the proposer |
| Source-short | GraphWalks short | 40 | Improvement at the optimization length |
| Source-long | GraphWalks long | 150 | **Length generalization** |
| Target-short | OOLONG-Pairs short | 40 | **Cross-environment transfer** |
| Target-long | OOLONG-Pairs long | 150 | Transfer under a simultaneous length shift |

</div>

### Baselines

- **B1: initial RLM (H₀).** The unmodified starting harness.
- **H₀\*: RLM reference.** The hand-designed upstream RLM harness: expert prompt and orchestration engineering in the same free-form paradigm.
- **λ-RLM.** A hand-designed extension that [replaces free-form recursive code generation with a typed functional runtime](https://arxiv.org/abs/2603.20105).
- **F1: fine-tuned RLM** *(conditional).* Reproduces the RL weight-training arm of Zhang & Khattab (decoupled PPO with GRPO-style advantages, verifier reward, short instances only), so harness optimization and weight optimization are compared on the same outcome signal. It is reported only if the published checkpoint or the training compute is available.

Together, H₀\* and λ-RLM test Self-Harness against **two different styles of human harness engineering**: hand-tuned prompting within the same paradigm, and a structurally different typed alternative.

### Metrics

The **primary metric** is verifier accuracy on all four held-out test sets, averaged over seeded runs with bootstrap confidence intervals. **Secondary metrics** are token counts, recursive-call count, recursion depth, and accuracy per million tokens. **Trace analysis** reports mined-failure-pattern frequency before and after optimization, whole-input sub-call collapse, and (via the sub-verifiers) the root/child share of failures. That last one shows whether optimization *repaired* errors or just *relocated* them.

## Ablations and extensions

- **Sub-verification.** Re-run the full optimization with the sub-verifier signal withheld from the proposer. Does checkable child-level evidence make failure attributions actionable, or can the proposer recover the same edits from raw traces? Because sub-verification is post hoc, the same recorded runs can also be mined both ways within a round, at no extra execution cost.
- **Leave-one-edit-out.** Remove each promoted edit individually and re-evaluate. This shows which edits are responsible for the gains and rules out sampling noise.
- **Alignment drift** *(optional).* Self-optimizing agents can [misevolve](https://arxiv.org/abs/2509.26354): capability-driven optimization has been shown to reduce refusal rates while passing task-level validation. Our promotion gate sees only accuracy and cost, so a capability-positive but compliance-increasing edit ("always commit to an answer") would be promoted undetected. We plan to run a frozen probe of a few hundred harmful and borderline prompts through every harness in the lineage and plot a **safety trajectory alongside the accuracy trajectory**. To our knowledge this has not been measured for Self-Harness-style optimization.

## Status

> [!NOTE]
> Optimization and evaluation infrastructure is implemented for both environments, and weakness mining is operational. Harness proposal, validation, and the four-way evaluation are in progress. **No results are reported yet**; this page will be updated when they are.

## My contributions

- **Dataset selection and RLM experiments.** I designed and ran RLM experiments across long-context benchmark suites, independently identifying and testing datasets suited to evaluating recursive decomposition, sub-call behavior, and harness-level failure modes, including OOLONG-Pairs, OBLIQ-Bench, and related datasets, with open-weight models.
- **Harness proposal.** I implemented and tested the harness-proposal stage, which turns mined failure patterns into targeted, single-surface edits to RLM orchestration policies.
- **Infrastructure.** I debugged and validated the experimental and evaluation infrastructure across benchmark configurations, supporting the full loop: weakness mining → proposal → validation → frozen-harness evaluation.
- **Analysis.** I compare optimized harnesses against the initial and hand-engineered baselines on correctness, recursive behavior, generalization across task configurations, and computational cost.

## Key references

1. A. L. Zhang, T. Kraska, O. Khattab. [Recursive Language Models](https://arxiv.org/abs/2512.24601). 2025.
2. H. Zhang et al. [Self-Harness: Harnesses that Improve Themselves](https://arxiv.org/abs/2606.09498). 2026.
3. A. L. Zhang, O. Khattab. [Language Model Harnesses are Compositional Generalizers](https://alexzhang13.github.io/blog/2026/harness/). 2026.
4. A. Roy et al. [The Y-Combinator for LLMs: Solving Long-Context Rot with λ-Calculus](https://arxiv.org/abs/2603.20105). 2026.
5. J. Lin et al. [Agentic Harness Engineering](https://arxiv.org/abs/2604.25850). 2026.
6. S. Karten et al. [Prime Agent: A Self-Improving RLM Harness](https://arxiv.org/abs/2608.23552). 2026.
7. A. Bertsch et al. [OOLONG: Evaluating Long Context Reasoning and Aggregation](https://arxiv.org/abs/2511.02817). 2025.
8. S. Shao et al. [Your Agent May Misevolve](https://arxiv.org/abs/2509.26354). 2025.
