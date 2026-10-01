+++
title = "Curriculum Vitae"
description = "CV of Mohammed Akram Khan Lodi: research in recursive language models, agent harnesses, and efficient AI."
path = "cv"
+++

**Mohammed Akram Khan Lodi** · Chennai, India  
<mdakram23741@gmail.com> · [akramlodi.com](https://akramlodi.com) · [GitHub](https://github.com/akramlodi) · [LinkedIn](https://www.linkedin.com/in/mdakramlodi)

## Research interests

Recursive self-improvement (RSI) · Agentic systems & harness engineering · Large language models (LLMs) & recursive language models (RLMs) · Long-horizon reasoning · Efficient AI systems

## Education

**B.S. Abdur Rahman Crescent Institute of Science and Technology**, Chennai, India  
*B.Tech in Computer Science and Engineering* · Jun 2024 – Jun 2027 (expected)  
Transferred from the University of Victoria

- **GPA 9.77 / 10 · Rank 1 in batch**
- Coursework: Analysis of Algorithms; Data Structures; Theory of Computation; Artificial Intelligence Techniques; Natural Language Processing; Operating Systems; Network Security and Cryptography; Discrete Mathematics

**University of Victoria**, Victoria, British Columbia, Canada  
*B.Eng in Computer Engineering (first year)* · Sep 2023 – Apr 2024

- GPA 7.89 / 9
- Coursework: Fundamentals of Programming with Engineering Applications; Matrix Algebra for Engineers; Calculus I; Calculus II

## Publications & papers under review

- **[Self-Harnessing Recursive Language Models](@/research/self-harnessing-rlms/index.md)**  
  William Stanford, **Mohammed Akram Khan Lodi**, Jaloliddin Boymakhammadov, Eliaz Calvar, Simon Coumes  
  *Submitted to the First Workshop on Meta Agents: Managing Agents that Manage Agents, NeurIPS 2026* (under review)

## Presentations

- **[The Carbon Cost of Reasoning: Benchmarking Energy Efficiency in Fine-Tuning Gemma 4 for Mathematical Logic](@/research/carbon-cost-of-reasoning/index.md)**  
  National AI Summit on Industry 5.0, 2026 · B.S. Abdur Rahman Crescent Institute of Science and Technology, Chennai  
  Paper ID AIS 052 · Accepted & presented, Apr 2026 · [Proceedings](https://drive.google.com/file/d/1SklCgsuYjd8BWy3lgWytgx1N1_dLAlR6/view?usp=share_link)

## Research experience

**Researcher, Self-Harnessing Recursive Language Models** · Jul 2026 – present  
*Algoverse AI Research Program* · Advisor: Dr. Simon Coumes

- Investigating automatic optimization of recursive language model (RLM) harnesses and their generalization across tasks and context lengths.
- Designed and evaluated RLM experiments across long-context benchmark suites, independently identifying and testing datasets suited to evaluating recursive decomposition, sub-call behavior, and harness-level failure modes, including OOLONG-Pairs, OBLIQ-Bench, and related datasets, using open-weight models.
- Contributed to the Self-Harness optimization pipeline, implementing and testing the harness-proposal workflow that converts recurring failure patterns into targeted modifications to RLM orchestration policies.
- Debugged and validated the experimental and evaluation infrastructure across benchmark configurations, supporting the full loop of weakness mining, harness proposal, proposal validation, and optimized-harness evaluation.
- Analyzed optimized harnesses against initial and manually engineered baselines on correctness, recursive behavior, generalization across task configurations, and computational efficiency.

**Independent Researcher, The Carbon Cost of Reasoning** · Apr 2026 – present  
*B.S. Abdur Rahman Crescent Institute of Science and Technology*

- Designed a controlled comparison of Full Fine-Tuning, LoRA, and QLoRA on Google's Gemma 4 E2B-it (~5.1B parameters) on GSM8K, measuring mathematical reasoning accuracy alongside environmental cost.
- Implemented energy and emissions tracking with CodeCarbon, recording GPU, CPU, and RAM energy (kWh) and estimated CO₂e per training run from the cloud region's grid carbon intensity.
- Designed reproducible experiments with fixed prompt templates, dataset splits, and evaluation protocols: Full FT, LoRA (ranks 8/16/32), and QLoRA (4-bit NF4; ranks 8/16/32) across core and ablation runs on AWS EC2 (NVIDIA A10G and L40S).
- Introduced the **Green Gap**, the point where marginal reasoning improvement per additional kWh declines sharply, and the **Reasoning Efficiency Index (REI)**, accuracy gained over zero-shot per kWh consumed.

## Industry experience

**Co-Founder, [Airbil](https://airbil.in)** (AI-native agency) · Dec 2025 – present

- Built multi-agent AI systems using LLMs, FastAPI, and PostgreSQL/Supabase for enterprise procurement, sales, and customer-engagement workflows.
- Developed agent evaluation and monitoring infrastructure to measure task performance, tool usage, failure modes, latency, and reliability.
- Engineered scalable agent orchestration and API integrations connecting ERP/CRM and messaging platforms into unified agentic workflows.

**Software Engineer, [Skillsync](https://skillsync.site)** (AI-powered hiring platform) · May 2025 – Jul 2025

- Developed an AI interviewer that conducts candidate interviews and generates automated candidate analysis and evaluation.
- Built AI-driven candidate assessment and an ATS for automated resume screening, candidate ranking, and structured evaluation.
- Designed a concurrent, worker-pool task scheduler in Node.js with fault-tolerant job queuing, processing 10,000+ asynchronous tasks per month with zero downtime.
- Re-architected the PostgreSQL schema and moved data access to normalized REST endpoints, cutting query latency by 50% under production load.

**Co-Founder, [PocketLink](https://www.instagram.com/pocketlink.co/)** (AI-powered link-in-bio platform) · Feb 2025 – Dec 2025

- Architected cloud infrastructure on Docker, Kubernetes, and Azure serving 20,000+ users.
- Designed distributed inventory and payment-processing pipelines handling concurrent checkouts with real-time data consistency.
- Deployed an LLM-based conversational system using Gemini and LangChain with per-user context orchestration.

## Projects

**[NDA Analyzer: Multi-Agent RAG System](@/projects/nda-analyzer/index.md)** · Apr 2026 · [code](https://github.com/akramlodi/NDA-Analyzer---multi-agent-orchestration)

- Four-agent LLM pipeline for employment-NDA analysis: clause extraction, loophole detection, legal-compliance verification, and fix generation.
- RAG over Indian legal statutes, NDA templates, and HR policies using ChromaDB and sentence-transformer embeddings.
- Full-stack app in Next.js and TypeScript with Gemini 2.5 Flash Lite, streaming responses over Server-Sent Events.

## Technical skills

- **Programming:** Python, TypeScript/JavaScript, C, SQL
- **AI/ML:** PyTorch, LangChain, RAG, embeddings, fine-tuning, LoRA, QLoRA, LLM APIs
- **AI systems:** AI agents, multi-agent systems, recursive language models, LLM harnesses
- **Web & backend:** Next.js, React, Node.js, FastAPI, PostgreSQL, Supabase, REST APIs
- **Cloud & infrastructure:** AWS, Azure, Docker, Kubernetes, GitHub Actions, microservices
