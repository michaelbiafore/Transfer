# DRAFT: Product Requirements Document — Agentic Equity Long/Short Analyst

**Product:** Agentic Equity Long/Short Analyst (an agentic research system built on Claude Code)
**Version:** 0.3 (draft)
**Owner:** Product / Research Engineering
**Status:** Draft for team review
**Date:** 2026-06-19
**Figures:** (1) the investment-philosophy wiki / persona knowledge bases; (2) the four operating modes via Claude Code agent teams / subagents; (3) the committee-of-agents architecture sketch

> This document specifies an agentic research system (Agentic Equity L/S, AELS) whose job is to perform, at the **90th percentile or better**, the **workflows** of a human equity long/short analyst: ingesting price data, SEC filings, earnings-call and management-interview recordings, macro and a wide variety of alternative data, from which it emits a reasoned, documented and interrogatable suggestion for a long/short portfolio. 

---

## Table of Contents

- [1. Overview](#1-overview)
- [2. Problem Statement](#2-problem-statement)
- [3. Product Objective](#3-product-objective)
- [4. Users](#4-users)
- [5. Goals and Metrics](#5-goals-and-metrics)
  - [Business Goals](#business-goals)
  - [Product Goals](#product-goals)
  - [Success Metrics](#success-metrics)
- [6. Product Principles](#6-product-principles)
- [7. Scope of v1](#7-scope-of-v1)
- [8. Required Data via MCP](#8-required-data-via-mcp)
- [Appendix 1. Team and Staffing](#appendix-1-team-and-staffing)
  - [Roles to be Hired](#roles-to-be-hired)
  - [Hire 1: Senior Platform and AI Systems Engineer](#hire-1-senior-platform-and-ai-systems-engineer)
  - [Hire 2: Senior Quant Research Engineer](#hire-2-senior-quant-research-engineer)
  - [Hire 3: Data Platform and Knowledge Graph Engineer](#hire-3-data-platform-and-knowledge-graph-engineer)

---

## 1. Overview

The product is an Agentic ELS Analyst system (AELS), designed to augment typical workflows of a hedge-fund equity **long/short analyst**. 

Architecturally, AELS is structured as a **committee** of ~3-12 'opinionated' equity L/S analyst sub-agents, each sub-agent is defined by a strong **'Persona'** aligned with the **investment philosophy** of one or more successful recently or currently active money managers. Parsing books, articles and letters written by the money manager and video interviews of the money manager into a knowledge base (e.g. a Karpathy wiki or graph database) provides the basis for each 'Persona'. 



![Figure 1: The investment-philosophy wiki / persona knowledge bases. The committee runs 3–12 persona agents, each aligned with a distinct investment methodology — factor-neutral long/short (Asness), deep value (Graham, Klarman), classic growth (Fisher, Cathie Wood), GARP (Lynch), quality value (Buffett–Munger, Terry Smith), behavioral (Dreman, Montier), out to an arbitrary persona N. Each persona is grounded in a per-investor knowledge base (WIKI_*) built from that investor's books, whitepapers, and interviews — materialized through pipelines such as /epub2md and /mp4+wizwhisp. The corresponding agent (e.g. AGENT_ClassicGrowth reading WIKI_Wood) reads its wiki while drawing on MCP servers, Skills, and subagents.](../../Input/elsj_prd_figs/investment_philosophy_wiki.png)



The AELS system operates in one of 4 modes, selectable by the user. The 4 modes are the product of a user choice of 2 **reasoning modes** and 2 **operational modes**. In the first 'reasoning mode' each Persona responds to the user's query according to the tenets of their investment philosophy and without be aware of, let alone consulting, other Persona agents working in parallel. In the second reasoning mode, the Personas share the results of intermediate tasks (e.g. the Einhorn and Klarman 'Personas' might share the results of an EPS calculation) and might even communicate directly on more substantive issues. Two Claude Code frameworks---**Agent Teams** and **Subagents** (and equivalent frameworks from OpenAI) directly support these communication patterns: 


![Figure 2: The four operating modes, realized through Claude Code agent teams and subagents. The user selects one of four modes formed by crossing two reasoning modes with two operational modes (one-off vs. always-on). Each persona runs as a Claude Code subagent under the Top Agent, so the committee can be expanded, pruned, or re-tasked per PM without rebuilding the system. See the Claude Code agent-teams documentation: https://code.claude.com/docs/en/agent-teams](../../Input/elsj_prd_figs/agent_teams_subagents.png)

The two **operational modes** are: (1) **one-off**, the AELS system responds to a query from the human analyst, and (2) **always-on**, the AELS system regularly monitors all sources that potentially affects its most recent recommendations and alerts the user to any material developments. Since 'always-on' mode will make substantial demands of IT (need to use high availability cloud services, like Kubernetes) the early versions will offer only "one-off" model. 

The Agentic ELS Analyst will be constructed to support a high degree of customization by the analyst/PM, allowing fine-tuning (in the colloquial, as well as technical sense) to the particular investment methodology of each PM, and **risk-calibrated** to each PM's personal definition of risk and their risk/return tradeoff.

---

## 2. Problem Statement

Fundamental equity research has historically been slow, fragmented, and difficult to keep continuously updated across large universes. Analysts must gather filings, transcripts, estimates, market data, alternative data, news, peer comparisons, and catalyst calendars, then synthesize them into actionable long/short views under time pressure.

Current workflows suffer from:

- High manual overhead for data collection and normalization.
- Reduced scalability across sectors and geographies due to human-limited bandwidth.
- **Bandwidth-driven omission of concerns** — a single human cannot look at , fundamentals, macro, supply-chain networks over multiple hops, real-time market microstructur and geospatial signals at once, so whole categories of evidence go unexamined.
- Inconsistent thesis maintenance after initiation ("always-on" mode)
- Slow reaction to new information due to volume of potentially relevant information. ("always-on" mode)
- Suboptimal reproducibility and detailed traceability back to evidence from conclusions.



---

## 3. Product Objective

Build an agentic analyst that can 
**increase analyst throughput** without degrading rigor, **outperforming roughly 90% of human analysts**, while remaining interrogatable by the user, observable for detailed after-the-fact analysis, secure, and cost-disciplined enough to run as a production platform.

---

## 4. Users

- **Human analysts** seeking faster coverage initiation and maintenance.
- **Portfolio managers**. 
---

## 5. Goals and Metrics

### Business Goals

- Expand effective research coverage per analyst.
- Reduce time from new event to updated view.
- Improve hit rate of surfaced opportunities versus the current process.

### Product Goals

- Deliver institutional-quality long and short memos.
- Provide evidence traceability for every material claim.
- Detect and escalate thesis-changing events quickly ("one-off" mode)

### Success Metrics

- **Uptake by human analysts and PMs:** as evidenced by Claude Code **/usage** statistics.
- **Time to first coverage memo:** 
- **Time to event-driven thesis refresh:** 
- **Citation coverage:** 


---

## 6. Product Principles


### Primary
- **Achieve 'variant perception' by design**---the architecture deliberately runs a committee of **opinionated 'Personas'** based on distinct but internally coherent investment 'philosophies'. Initially these 'Personas' will be based on successful investors rather than academic theories. 
- **Exploit agent parallelism fully**: Traditional human analysts have limited-bandwidth and were therefore compelled to largely omit from consideration relevant, but secondary factors: everything from details of the macro environment to real-time analysis of bid/ask and other market microstructure dynamics. Agent parallelism offers the opportunity to bring these contextualizing phenomena back into the equity L/S analysis process at an earlier stages. 
- **Build a 'template', not an immutable app.** While the system will ship with default settings, it is meant to be  fine-tuned per PM methodology and risk-calibrated to each PM's risk/return tradeoff, for example by deleting or adding Agent Personas to the top level committee of agents. 

### Secondary
- **High evidence traceability.** Every material conclusion cites underlying source and provides backlinks.
- **Staleness.** All evidence comes with accessible staleness metadata.
- **Explicit uncertainty.** Contemporary methodologies like conformal prediction provide grounded error bars. 
- **Continue dissolving the quant-versus-discretionary divide.** Established quantitative methods---e.g. 'correlation' variants that can capture non-linear relationships---are available to all agents for application to any pair of numerical series.
- **Multimodal to the core.** A good analyst reads PDFs, looks at the color graphics, and relates them to the text; the system must do the same (vision-capable RAG over charts, tables, and exhibits — see [FR7](#fr7-multimodal-and-network-reasoning)).
- **Governed, observable, secure, and cost-disciplined** are first-class product attributes, not afterthoughts (Section 12).

---

## 7. Scope of v1

**Included**

- Variant perception analysis via 'committee of subagents'.
- Catalyst detection and calendarization, in particular aim to achieve global multilingual coverage of all influential sources, including alternative media. 
- Audit trail for data, prompts, tools, and outputs.
- Coverage initiation memo generation.
- Continuous monitoring and thesis refresh (in "always-on" mode).
- Risk flagging and thesis-break detection.


**Excluded**

- Cross-asset and macro books.
- Full portfolio optimization.


---

## 8. Required Data via MCP

The system assumes agent-friendly MCP (or CLI) access to all required data and tools.

**Market and pricing data**

- Real-time and end-of-day equity prices; OHLCV history.
- Corporate actions: splits, dividends, spin-offs, symbol changes.
- Sector, industry, index, factor, and benchmark membership.
- Borrow availability, borrow cost, utilization, short interest.
- Options-implied metrics where available, including implied volatility and skew.
- FX rates for multinational normalization.

**Fundamental company data**

- Income statement, balance sheet, cash flow statement, segment data.
- Standardized historical fundamentals by quarter and year.
- Consensus estimates: revenue, EBITDA, EPS, FCF, margins, segment estimates.
- Estimate revision history and dispersion; guidance history.
- Sector-specific KPIs such as ARPU, subscribers, utilization, same-store sales, bookings, take rate, or wafer starts.

**Primary source documents**

- SEC and global regulatory filings: 10-K, 10-Q, 8-K, 20-F, proxy, registration statements, insider filings.
- Earnings-call transcripts and prepared remarks.
- Investor presentations and conference transcripts.
- Press releases and company website IR materials.
- Capital-markets-day materials.

**News and event data**

- Real-time cable news and social media feeds.
- M&A announcements; management changes.
- Product launches, recalls, litigation, investigations, and regulatory actions.
- Supply-chain disruptions, plant outages, and geopolitical events.

**Alternative and channel data**

- Web traffic and app usage trends.
- Job postings and hiring velocity.
- Credit/debit card panel trends where legally licensed.
- Geolocation, store traffic, or footfall.
- Review sentiment and product-ranking data.
- Patent, shipping, import/export, and satellite-derived signals where relevant.
- Expert-network summaries if permitted and compliant.

**Ownership and positioning data**

- Institutional ownership; insider buying and selling.
- Fund holdings and changes where available.
- Short-interest history.
- Prime-brokerage and crowding proxies if licensed.

**Reference and knowledge data**

- Industry maps and taxonomies.
- Historical research memos and model notes.
- Internal playbooks on successful long/short patterns.
- Compliance rules, restricted lists, and disclosure requirements.
- Entity resolution and security master.

**Calendar and workflow data**

- Earnings dates; investor events and conferences.
- Lockup expirations; regulatory milestones.
- Internal coverage assignments and approval queues.
- **Forward catalyst calendars**, including non-traditional sources (e.g. investigative YouTube channels such as Coffeezilla for short-side catalysts).

**Network and geospatial data**

- Economic-link and supply-chain network graphs (customer/supplier, ownership, board interlocks) — the basis for the **Neo4j**-backed network layer in [FR7](#fr7-multimodal-and-network-reasoning).
- Real-time geophysical and environmental feeds (e.g. USGS earthquake monitor, El Niño / weather) to drive economic-links-and-predictable-returns style signals.


## Appendix 1. Team and Staffing

The team is a small, cross-functional **"investment engineering" pod**, not a loose collection of specialists. For a system meant to do institutional long/short work, the team must cover idea generation, fundamental research, risk-aware evaluation, data/knowledge architecture, and production operations in one loop. Two principles govern hiring:

- **T-shaped people.** Each member needs real depth in one discipline *and* the ability to overlap with two others. 


### Roles to be Hired

Recommendation is to hire **3 people by default**, with a lean 2-hire version possible and a 4th hire justified only for rapid scaling or heavy internal adoption. The recurring themes for every remaining role are **production rigor, realistic backtesting, MCP-style data/tool exposure, and ongoing optimization of both token spend and local-model inference economics.**

### Hire 1: Senior Platform and AI Systems Engineer

*Priority 1. Why it remains missing: someone must convert PM-led prototypes into resilient services with observability, deployment hygiene, and inference/runtime controls.*

This CV should read like a **production ML-platform owner who also understands inference economics** — not generic DevOps. Target **7–12 years** across backend/platform/ML systems, with at least a few years operating model-serving or retrieval-heavy systems in production, not just internal prototypes.

What the person's CV should contain:

- Titles like Staff Backend Engineer, ML Platform Lead, AI Infrastructure Engineer, or Principal Systems Engineer.
- Shipped distributed Python services, model gateways, queue-based pipelines, and containerized deployments, with observability tied together under **OpenTelemetry** (traces, metrics, logs).
- Concrete ownership of **Kubernetes** or equivalent orchestration, CI/CD, secret management, rate limiting, retries, circuit breakers, and service SLOs.
- Hands-on experience with fast local-inference stacks such as **vLLM** or similar, where throughput gains come from PagedAttention, continuous batching, chunked prefill, and prefix caching.
- Evidence of **cost controls**: request routing by model tier, caching, batch/offline inference, token budgeting, and GPU-utilization optimization.
- Experience supporting **both API-hosted and local/self-hosted models**, with a clear decision framework for when privacy, latency, or unit economics favor local inference.

This person should be able to say: *"this agent step goes to a small local model, this one goes to a frontier API model, this path gets memoized, and here is the telemetry proving the tradeoff."* GenAI telemetry conventions (model, prompt/completion usage, token-count attributes) are exactly what is needed for cost and performance governance.

### Hire 2: Senior Quant Research Engineer

*Priority 2. Why it remains missing: even with a quant PM in seat, the team needs a dedicated builder for simulation, bias controls, attribution, and evaluation pipelines.*

This CV should look like a **research implementer with strong anti-self-deception instincts.** The PM can define what matters economically; this hire builds the machinery that prevents the organization from fooling itself with overfit simulations or unrealistic paper alpha. A good profile is **4–8 years** in quant research, systematic equity, portfolio analytics, or execution/risk research, with code ownership in Python and a habit of turning verbal investment hypotheses into testable designs. They need enough market intuition to know when a result is statistically interesting yet operationally useless.

What the CV should contain:

- Built event studies, factor-aware diagnostics, and walk-forward or out-of-sample evaluation pipelines.
- Explicit modeling of commissions, slippage, market impact, liquidity, order types, and data-quality artifacts. (Neglecting transaction costs is a classic error that often matters materially to bottom-line performance.)
- Experience with attribution, hypothesis management, and **experiment registries**, so the team can compare agent-generated ideas against benchmarks like sell-side consensus, factor exposures, and PM prior beliefs.
- Familiarity with text-derived signals and document-event alignment, since the agent system generates features and candidate theses from filings, transcripts, and narrative data.

This person owns the question: **"did the system actually add investable information?"** That is a different job from general MLOps, and it remains essential even with a strong PM already inside the pod. In the build, this hire is the guardian against the failure mode where the agent system *sounds* insightful but cannot demonstrate robust out-of-sample value.

### Hire 3: Data Platform and Knowledge Graph Engineer

*Priority 3. Why it remains missing: someone must own the research data plane — document ingestion, entity/event normalization, MCP-facing tool contracts, lineage, caching, and graph-backed retrieval quality.*

This CV should read like a **research data engineer for unstructured and semi-structured financial information.** Since the PM already has GraphRAG and document-pipeline experience, this hire complements that by building durable ingestion, canonical schemas, and retrieval-serving layers rather than inventing the conceptual approach from scratch. Target **5–10 years** in data engineering, search/retrieval engineering, or knowledge-graph platforms, with evidence of handling entities, aliases, temporal updates, document provenance, and entitlement-aware access. (Finance research is full of messy joins across issuers, tickers, subsidiaries, events, transcripts, PDFs, estimates, and vendor feeds — the CV should show they have tamed that kind of mess before.)

What the CV should contain:

- Strong ingestion and normalization work over filings, transcripts, research documents, tabular feeds, and metadata.
- Experience designing graph or graph-adjacent schemas (e.g. **Neo4j**) for entities, relationships, events, and evidence provenance — the substrate for the network reasoning in [FR7](#fr7-multimodal-and-network-reasoning).
- Experience exposing data and tools through **MCP-style, gateway-like** governed interfaces, so agents use the data plane through a standardized layer rather than bespoke integrations.
- Practical cost awareness: chunking choices, embedding-refresh strategy, cache design, deduplication, and selective reprocessing, so the team does not pay repeatedly for low-value compute.

This role becomes important as soon as the system goes beyond a single PM's personal workflow and needs multiple agents, users, and data products to behave consistently. It is also the role most likely to keep the token bill from exploding, through better retrieval, pruning, and caching **upstream** of generation.
