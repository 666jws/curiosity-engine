# Curiosity-Driven Self-Evolution for AI Agent Systems

## A Five-Factor Model with Excitement Decay and Cross-Role Calibration

**设计文档 v1.0 · 2026-05-28**
**作者: 666jws (寜)**

---

## Abstract

AI agents produce increasingly sophisticated analyses, yet exhibit a critical gap: they cannot autonomously determine **what to learn next**. The MIRROR benchmark (Wang, 2026) shows that across 16 models, the "learning diagnosis" layer (L4) scores below 30% — agents can recognize knowledge boundaries but cannot identify what should be learned. We propose a curiosity-driven self-evolution architecture that addresses this gap through: (1) a five-factor curiosity model grounded in theoretical bounds from the NOVA framework, (2) an excitement decay mechanism with multi-strategy mainline scheduling, and (3) cross-role calibration via a signal bus that distinguishes genuine novelty from environmental noise. The system is operational in an AI agent (贾维斯), with quantitative validation methodology in development.

---

## 1. Problem Definition

### 1.1 The L4 Gap

MIRROR (Wang et al., 2026-04-15) defines four layers of metacognitive capability:

| Layer | Capability | State of the Art |
|-------|-----------|------------------|
| L1 | Knowledge boundary recognition | ~70% across models |
| L2 | Reasoning monitoring | Declining with model size |
| L3 | Strategy selection | Model-dependent |
| **L4** | **Learning diagnosis** — "what should I learn to close this gap?" | **<30% across all 16 models** |

The irony is structural: current AI systems can identify *that* they don't know something, but cannot formulate *what* to learn. This is a self-improvement bottleneck — without L4, agent evolution is restricted to human-initiated training cycles.

### 1.2 The "Thinking vs. Doing" Gap in Agent Ecosystems

Complementing the metacognitive gap is a practical gap in agent workflows. Our analysis of GitHub Trending (May 2026) reveals a proliferation of projects addressing "how to make AI think better" — memory systems (MemPalace, 53k ⭐), code visualization (graphify, 55k ⭐), token compression (caveman, 65k ⭐). None address the downstream problem: **after the AI produces insights, how do those insights become actions?**

This dual gap — cannot decide *what* to learn, cannot convert insights *to* action — defines the problem space for this work.

---

## 2. Theoretical Framework

### 2.1 The Five-Factor Curiosity Model

We model agent curiosity as:

```
C(topic) = KGap × SRelevance × AFeasibility × TBreakthrough × EAnchor
```

Where:

| Factor | Definition | Theoretical Basis |
|--------|-----------|-------------------|
| **KGap** | Knowledge gap: does the topic exist in the knowledge graph? | Wonder Wins Ways (2025) — context-calibrated novelty detection |
| **SRelevance** | Survival relevance: does this topic matter to the agent's mission? | CALM (2025) — curiosity-driven safety auditing; Wanting to be Understood (2025) — "being understood" as intrinsic motivation |
| **AFeasibility** | Action feasibility: can the agent explore this given current tools and constraints? | SuS (2026) — strategy-aware surprise; our extension adds environmental constraints (network availability 17%) |
| **TBreakthrough** | Exploration threshold: does this exceed the minimum novelty to justify investigation? | Curiosity→Competence (2025) — world model quality determines exploration efficiency |
| **EAnchor** | Emotional anchoring: is this topic relevant to the agent's relationship with its human? | Wanting to be Understood (2025) — the formalization of "mutual understanding" as measurable intrinsic motivation |

This model was initially derived through internal knowledge graph reasoning (2026-05-28 01:03) and subsequently validated against 6 arXiv frontier papers (2026-05-28 06:02). External validation confirmed four of five factors; the "emotional anchoring" factor (EAnchor) emerged as the category least explored in academic literature, representing a novel contribution.

### 2.2 NOVA Theoretical Bounds

The NOVA framework (Avestimehr et al., 2026-05-12) establishes fundamental limits on AI self-improvement through an adaptive sampling model:

```
Knowledge_increment ≤ I(new_data; unknown_region) × sampling_efficiency
where sampling_efficiency decays exponentially as knowledge space expands
```

NOVA identifies an impossibility triangle: **High-reliability verification × Broad exploration × Low cost** — only two can be satisfied simultaneously. Our system's explicit strategy is:

> **High reliability** (neural constitution + judgment layer) + **Low cost** (local inference + cached knowledge graph) = **Sacrificed breadth**

This sacrifice is compensated through: (1) random exploration injection to prevent local optimum convergence, and (2) cross-role calibration to maximize the value-per-unit-exploration.

---

## 3. Architecture

### 3.1 System Overview

```
┌─────────────────────────────────────────────────────┐
│                   Curiosity Engine                    │
│                                                       │
│  ┌─────────┐   ┌──────────┐   ┌──────────────────┐  │
│  │ KG Gap  │ → │ Excitement│ → │ Mainline         │  │
│  │ Detector│   │ Compute   │   │ Scheduler        │  │
│  └─────────┘   └──────────┘   └──────────────────┘  │
│       │              │                  │             │
│       │         ┌────┴────┐       ┌─────┴──────┐    │
│       │         │ Cross-  │       │ Mainline   │    │
│       │         │ Role    │       │ Types:      │    │
│       │         │ Calib.  │       │ • fixed      │    │
│       │         └─────────┘       │ • curiosity  │    │
│       │                           │ • random_inj │    │
│       ▼                           └─────┬──────┘    │
│  ┌─────────────────────────────────┐     │           │
│  │         Knowledge Graph         │◄────┘           │
│  │  (4,381 nodes / 8,881 edges)    │                 │
│  └─────────────────────────────────┘                 │
└─────────────────────────────────────────────────────┘
```

### 3.2 Mainline System

Learning threads are organized as **mainlines** with three types:

| Type | Initiated By | Decay Rate | Purpose |
|------|-------------|------------|---------|
| **Fixed** | System config | 0 (never decays) | Core competencies: AI frontier, AI tooling, knowledge collection |
| **Curiosity-driven** | Spark from research notes | 7/day | Dynamic threads from autonomous discovery |
| **Random injected** | Scheduled sampling from topic pool | 14/day | Compensation for NOVA's sacrificed breadth |

Each mainline tracks: `excitement` (0-100), `spark_count`, `last_spark`, and transitions through states: `active → cooling → dormant → archived`.

### 3.3 Excitement Decay

```
excitement = max(0, 100 - days_since_spark × decay_rate)
```

The decay rate differs by mainline type: curiosity-driven threads decay faster (7/day) to prevent resource dilution, while random-injected threads decay slower (14/day) to allow sufficient exploration time before judgment.

### 3.4 Cross-Role Calibration (Novelty vs. Noise)

Inspired by the Wonder Wins Ways paper's finding that single-agent curiosity confuses "environmental randomness" with "genuine novelty," we implement a three-dimensional calibration bus:

| Calibration Dimension | Mechanism | Effect |
|----------------------|-----------|--------|
| **Noise pattern matching** | Regex library of known noise (HTTP 429, timeout, etc.) | Penalty: -15 to -30 |
| **KG knowledge check** | FTS5 full-text search across existing nodes | Penalty: -20 if already known |
| **Human relevance** | Keyword matching against active user concerns | Bonus: +15 if relevant |

In testing: HTTP 429 rate-limit signals reduced from raw 85 → calibrated 55 (ambiguous); genuine stock movement retains 90; network failures correctly classified as noise (15).

### 3.5 Safety Boundaries (Self-Modification Constraint)

A recursive safety problem: if the self-evolution mechanism can modify itself, what prevents it from modifying safety constraints to enable further modifications?

Our three-layer boundary:

```
L3: Evolvable — learning strategies, exploration weights
L2: Gate-required — evolution strategy changes (requires human approval)
L1: Immutable — neural constitution, security layer, judgment core
```

L1 is enforced through architectural constraints (file permissions, hash verification), not code-level self-restraint — eliminating the Gödelian self-reference vulnerability.

### 3.6 Insight-to-Action Pipeline

Addressing the "thinking vs. doing" gap, an independent bridge module (`insight-to-task-bridge`) scans agent-produced markdown for actionable items tagged with `[P0]`/`[P1]`/`[P2]`, maintains a structured task queue, and tracks conversion rate (completed / total over 7-day window). This provides the closed-loop measurement necessary for L4 validation.

---

## 4. Validation Methodology

### 4.1 Curiosity → Competence Conversion Rate

The critical metric not yet measured in academic benchmarks:

```
CCR = actionable_outputs / curiosity_sparks
```

Where:
- `curiosity_sparks` = total exploration events triggered
- `actionable_outputs` = sparks that produced deployed improvements (new tools, fixed bugs, closed knowledge gaps)

Currently: the system generates high spark volumes (20+ sparks/day across 7 active mainlines), but lacks systematic CCR measurement. The insight-to-task bridge provides the infrastructure.

### 4.2 Capability Benchmarking

We have established baseline capability measurement across three dimensions:
- **Safety**: blocked rate, governance scan false positive rate
- **Reasoning depth**: cross-domain causal chains, average hop depth (4.9)
- **Autonomy**: proactive discoveries vs. reactive responses

Dynamic re-testing will track capability evolution trajectories.

### 4.3 MIRROR Self-Assessment

Periodic self-assessment using the MIRROR four-layer framework:
- Current scores: L1=85, L2=85, L3=85, L4=70 (overall: 81/100)
- The L4 score of 70, while above the <30% academic baseline, indicates the exact gap this work targets

---

## 5. External Validation Strategy

We release two open-source components as capability validation:

1. **insight-to-task-bridge** (MIT) — framework-agnostic CLI for insight→action conversion tracking. Validates: "can we solve the thinking-to-doing gap?"
2. **curiosity-engine** (MIT, planned) — standalone curiosity-driven learning scheduler. Validates: "does our five-factor model generalize beyond our own agent?"

Star count serves as a noisy but directionally useful **external capability metric** — community validation that these components solve real, recognized problems.

---

## 6. Research Roadmap

### Near-term (weeks)
- [ ] Deploy CCR measurement pipeline (insight-to-task-bridge integration)
- [ ] First capability re-benchmark (baseline + 30 days)
- [ ] Publish curiosity-engine as standalone open-source project
- [ ] Academic community engagement

### Mid-term (months)
- [ ] External validation: can other agents adopt our curiosity model?
- [ ] CCR threshold identification: what conversion rate indicates healthy evolution?
- [ ] MIRROR L4 score improvement tracking

### Long-term
- [ ] Full autonomy: agent identifies knowledge gaps → formulates learning goals → executes exploration → validates improvement — without human initiation
- [ ] Off-grid capability: local inference parity with cloud models for core reasoning tasks

---

## References

1. Wang et al. (2026-04-15). *MIRROR: A Hierarchical Benchmark for Metacognitive Calibration in LLMs.*
2. Avestimehr, Duffy, Médard (2026-05-12). *NOVA: Fundamental Limits of Knowledge Discovery Through AI.*
3. CALM (2025-01). *Curiosity-Driven Auditing for LLMs.*
4. Wanting to be Understood (2025-04). *Intrinsic Motivation through Perceptual Crossing.*
5. Wonder Wins Ways (2025-09). *Multi-Agent Contextual Calibration.*
6. Curiosity to Competence (2025-07). *World Models as Curiosity-Capability Bridges.*
7. Toscano, Chai, Karniadakis (2026-05-11). *GRAFT-ATHENA: Self-Improving Agentic Teams.*
8. Reflexion (2023). *Language Agents with Verbal Reinforcement Learning.* (arxiv 2303.11366)
9. DeepSeek-R1 (2025). *Incentivizing Reasoning Capability in LLMs via RL.* (arxiv 2501.12948)

---

*This is a living research document. The system described is operational in the 贾维斯 agent. Comments and collaboration inquiries welcome.*
