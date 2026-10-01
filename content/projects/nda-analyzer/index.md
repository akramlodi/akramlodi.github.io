+++
title = "NDA Analyzer: a Multi-Agent RAG System"
description = "Four LLM agents that extract clauses from employment NDAs, find loopholes, check them against Indian law, and propose fixes, grounded with retrieval."
date = 2026-04-15

[taxonomies]
tags = ["multi-agent", "RAG", "LLM"]

[extra]
toc = true
+++

<ul class="link-row">
<li><a href="https://github.com/akramlodi/NDA-Analyzer---multi-agent-orchestration">Code on GitHub</a></li>
</ul>

## What it does

Employment NDAs are long, templated, and easy to sign without reading. NDA Analyzer reads one for you and returns a clause-by-clause review: what each clause says, where it's loose or one-sided, whether it holds up against Indian law and common HR policy, and how to rewrite it.

## Architecture

The work is split across **four agents that run in sequence**, each with a narrow job and its own prompt:

1. **Clause extraction.** Segments the document into individual clauses and labels them.
2. **Loophole detection.** Flags ambiguous, overly broad, or one-sided language in each clause.
3. **Legal-compliance verification.** Checks flagged clauses against retrieved statutes and policies.
4. **Fix generation.** Proposes concrete rewrites for clauses that fail.

Splitting the task this way keeps each agent's context small and its failures easy to localize. That same idea is what drew me to [harness engineering for recursive language models](@/research/self-harnessing-rlms/index.md).

## Grounding with retrieval

The compliance and fix agents are grounded with a **retrieval-augmented generation** pipeline: Indian legal statutes, NDA templates, and HR policy documents are embedded with sentence-transformers and stored in **ChromaDB**, and the relevant passages are retrieved for each clause before the model judges it.

## Stack

Next.js and TypeScript on the front end, **Gemini 2.5 Flash Lite** for the agents, REST APIs between stages, and **Server-Sent Events** to stream each agent's output as it's produced.
