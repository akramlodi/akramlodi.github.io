+++
title = "Building an AI NDA Analyzer"

description = "What building a multi-agent legal analysis system taught me about AI orchestration, RAG, and designing reliable LLM applications."

date = 2026-07-08

[extra]
banner = ""
toc = true
toc_inline = true
toc_ordered = true
featured = true

[extra.comments]
host = ""
user = ""
id = ""
+++

## The Problem

Employment contracts are one of those things that almost nobody actually reads.

Even if someone does read them, legal language is difficult to understand, and identifying whether a clause is unfair or even enforceable requires knowledge of employment law.

I wanted to see whether a team of specialized AI agents could perform this analysis automatically instead of relying on one large prompt.

Rather than asking a model to "analyze this NDA," I wanted to build a pipeline where each AI agent had a single responsibility.

Live project: https://nda-analyzer.akramlodi.com

## The Solution

I built an AI-powered NDA Analyzer focused on Indian employment NDAs.

Users simply upload a PDF or DOCX file and receive a structured report highlighting:

- Potential loopholes
- Compliance gaps under Indian law
- Risk level for each clause
- Suggested improvements
- Executive summary

Instead of using one massive prompt, the application uses four specialized AI agents working together.

1. **Clause Extractor** identifies and structures every clause.
2. **Loophole Finder** searches for ambiguous or risky wording using Retrieval-Augmented Generation (RAG).
3. **Legal Comparator** checks every clause against relevant Indian legal statutes.
4. **Fix Suggester** proposes safer alternatives while generating an overall risk assessment.

Breaking the problem into smaller tasks dramatically improved consistency and made debugging significantly easier.

## Building the Pipeline

One of the biggest challenges wasn't generating text—it was making LLM outputs reliable.

Each agent was designed to return structured JSON instead of free-form responses. That allowed downstream agents to consume previous outputs without brittle parsing.

To improve factual accuracy, I implemented Retrieval-Augmented Generation using ChromaDB as the vector database.

Three separate knowledge collections were created:

- Indian legal statutes
- NDA clause templates
- Company HR policies

Instead of relying solely on model knowledge, each agent retrieved only the information relevant to its task before generating an answer.

This significantly reduced hallucinations while making the analysis more grounded.

Another interesting challenge was user experience.

Instead of making users wait until the entire pipeline completed, I streamed the progress of each agent using Server-Sent Events (SSE). Watching each stage complete in real time made the application feel much faster, even though the total processing time remained roughly the same.

## Tech Stack

The project was built using:

- Next.js App Router
- TypeScript
- Tailwind CSS
- Gemini 2.5 Flash Lite
- HuggingFace sentence-transformer embeddings
- ChromaDB
- PDF and DOCX parsing
- Server-Sent Events (SSE)

The architecture intentionally separates document parsing, retrieval, prompting, orchestration, and presentation into independent modules, making it easy to extend with additional agents in the future.

## Lessons Learned

### AI applications are mostly systems problems.

Prompting is only one small piece.

Reliable AI products require orchestration, structured outputs, retrieval systems, error handling, streaming, caching, and thoughtful UX.

### Specialized agents outperform one giant prompt.

Giving each agent one well-defined responsibility produced more reliable outputs than asking a single model to solve everything at once.

It also made prompt iteration much easier because individual components could be improved independently.

### Retrieval is essential.

Without RAG, the model often relied on memorized knowledge that was incomplete or outdated.

Providing relevant legal references dramatically improved factual consistency while reducing hallucinations.

### Structure matters.

Generating predictable JSON responses instead of paragraphs made the entire pipeline much more robust.

It also enabled each agent to build on previous outputs without complicated parsing logic.

### AI products should explain their reasoning.

Simply saying that a clause is "bad" isn't very useful.

Showing the legal basis, explaining the issue, assigning a severity level, and suggesting a concrete fix makes the output actionable and builds user trust.

{% alert(tip=true) %}

One of the biggest takeaways from this project was realizing that building AI applications is less about writing clever prompts and more about designing reliable systems around large language models.

{% end %}