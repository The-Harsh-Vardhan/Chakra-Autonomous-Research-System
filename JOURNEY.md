# The Journey — How Chakra Was Built

*From a single Kaggle notebook to a cyclic autonomous research system.*

---

## Origin: The Spark

It started with one question:

> Can a machine improve its own model — without a human touching the code between runs?

The answer came from [Andrej Karpathy's `autoresearch`](https://github.com/karpathy/autoresearch) — a brilliantly minimal benchmark where an AI agent edits a single training script, runs 5-minute experiments, and keeps only improvements. Three files. One editable surface. A fixed benchmark. And a loop that runs overnight.

That concept was the seed. But I wanted to go further:

- What if the system wasn't limited to one model file?
- What if it could handle *any* research domain — vision, NLP, tabular ML?
- What if every stage of the research process was structured, tracked, and auditable?

That curiosity became Chakra.

---

## Phase 1: Single-Domain Tool (HNDSR Super-Resolution)

The first version was purely practical: a pipeline for satellite image super-resolution using SR3 diffusion models. It was built for a university project (HNDSR — High-resolution Natural Development for Satellite Reconnaissance using Vision Research).

**What existed:**
- A training script for SR3 diffusion models
- A Kaggle notebook for cloud execution
- Manual config management
- W&B tracking bolted on as an afterthought

**What I learned:**
- Config management matters more than model architecture in practice
- The "run it on Kaggle" workflow needed structure — version control for notebooks, not just code
- W&B tracking should be a first-class citizen, not a side channel

**Key engineering decisions:**
- Introduced `base.yaml` + `inherits:` config inheritance
- Built `NullTracker` fallback so the system works without W&B
- Created version-stamped notebooks with embedded subprocess calls

---

## Phase 2: The Generalization (Domain-Agnostic Platform)

The single-domain tool worked, but it was **tightly coupled**. Every function knew it was doing super-resolution. Adding NLP or tabular classification would mean duplicating the entire pipeline.

This phase was a full architectural refactor:

### The Protocol Pattern

I defined `DomainLifecycleHooks` — a Python Protocol with 14 methods that every domain must implement. The core engine calls these hooks without knowing what domain it's running.

```python
class DomainLifecycleHooks(Protocol):
    def version_stem(self, version: str) -> str: ...
    def resolve_version_paths(self, version: str) -> VersionPaths: ...
    def build_version_configs(self, version, parent, lineage) -> dict: ...
    # ... 11 more methods
```

### The Domain Registry

Instead of hardcoding domains, I built auto-discovery. Drop a `domain.yaml` manifest into `domains/your_domain/` and the system finds it automatically:

```yaml
name: tabular_cls
display_name: Tabular Classification
primary_metric: accuracy
metric_direction: higher_is_better
entrypoints:
  lifecycle: chakra.domains.tabular_cls.lifecycle
  train_runner: chakra.domains.tabular_cls.train_runner
```

### Proof Points

I built three domains to prove the architecture:

| Domain | Task | Why It Matters |
|--------|------|----------------|
| **`hndsr_vr`** | Satellite super-resolution | The original domain — complex, GPU-heavy, real research |
| **`nlp_lm`** | Character-level language model | Different modality: text, not images. Different metrics (BPB, not PSNR) |
| **`tabular_cls`** | Tabular classification (Iris/Titanic) | Simplest possible domain — proves the pattern is lightweight |

**Engineering lessons from this phase:**
- Generic code that calls domain-specific hooks through a Protocol is far easier to maintain than framework-style inheritance
- Config inheritance (`inherits:` key with deep merge) eliminated 80% of config duplication
- `@runtime_checkable` Protocol + isinstance guards make the dispatch safe

---

## Phase 3: The Chakra Identity

By this point, the system worked but had no soul. It was "AutoResearch by Harsh Vardhan" — a technically correct but forgettable name.

The identity evolution was deliberate:

### The Cyclic Philosophy

Research isn't linear. Every experiment loops back:

```
Plan → Execute → Guard → Review → Improve → Repeat
```

This maps to the Hindu concept of **Chakra** (चक्र) — the wheel that never stops turning. Each stage got a Sanskrit name that reflects its role:

| Stage | Chakra Name | Meaning |
|-------|-------------|---------|
| Plan | **Sutra** (सूत्र) | Thread — the plan that holds the experiment together |
| Execute | **Yantra** (यन्त्र) | Instrument — the engine that produces results |
| Guard | **Rakshak** (रक्षक) | Guardian — protects integrity by validating contracts |
| Review | **Vimarsh** (विमर्श) | Reflection — examines what happened and why |
| Improve | **Manthan** (मन्थन) | Churning — extracts insight, proposes what's next |

A complete loop is an **Aavart** (आवर्त) — a revolution of the wheel.

### The Non-Negotiable Rule

Every Chakra term MUST have an English equivalent. The identity provides richness. English provides clarity. Both coexist everywhere.

### What Changed Technically

- Package renamed from `autoresearch_hv` → `chakra`
- New `chakra` CLI with stage-named aliases (`chakra sutra`, `chakra yantra`, etc.)
- `ChakraLogger` — structured stage-aware logging with emoji prefixes
- `chakra aavart` — one-command full cycle orchestrator
- README rewritten as a showcase document

---

## What I Learned

### 1. Separation of Concerns Scales

The `prepare.py` / `train.py` / `program.md` separation from Karpathy's original repo taught me the most important lesson: **separate the benchmark from the research surface from the research process.** In Chakra, this became:

- **Benchmark:** Domain manifests + config contracts + benchmark registries
- **Research surface:** Domain-specific models, datasets, metrics
- **Research process:** Core lifecycle + Chakra CLI + structured reviews

### 2. Configs Are the Real Interface

I spent more time on config design than model design. The `inherits:` system, three-variant structure (control/smoke/train), and frozen ablation plans prevent goal-drift during autonomous runs. This is infrastructure engineering, not ML engineering — and it matters more.

### 3. Tests Protect Refactors

The 47-test suite saved me during the Phase 2 refactor. When I extracted HNDSR-specific logic into the domain plugin pattern, tests caught every broken import, every missing method, every config path regression. Without them, the refactor would have been terrifying.

### 4. Defensive Fallbacks Build Trust

`NullTracker` was one of the best decisions. The system works without W&B, without torch (for core tests), without Kaggle, without a GPU. Every optional dependency has a clean fallback. This makes the system trustworthy in any environment.

### 5. Identity Matters

"AutoResearch" was forgettable. "Chakra" is memorable. The Sanskrit names don't just sound different — they force a mental model. When someone says "run the Aavart," they're thinking about the complete cycle, not a shell command. Naming is architecture for humans.

---

## What's Next — The Long-Term Vision

Chakra today is a structured, cyclic, domain-agnostic research engine that a human operates. The long-term vision is a system that operates itself.

### Near-Term (Next 3 Months)
- [ ] First complete Aavart cycle run end-to-end with real metrics
- [ ] Agent integration: LLM reads `Manthan` suggestions and generates `Sutra` for next version
- [ ] Multi-seed ablation: run 3+ seeds per experiment for statistical confidence
- [ ] Dashboard: web UI showing cycle history, metric trends, ablation lineage

### Medium-Term (6-12 Months)
- [ ] Autonomous Aavart: system proposes, runs, evaluates, and decides without human input
- [ ] Cross-domain learning: insights from one domain inform another
- [ ] Distributed execution: farm Yantra stages to cloud GPUs
- [ ] Paper generation: auto-generate LaTeX research reports from review findings

### Long-Term (The Dream)
- [ ] Full overnight research: leave it running, wake up to a better model
- [ ] Collaborative agents: multiple AIs debate experiment design
- [ ] Benchmark evolution: the system proposes harder benchmarks for itself

The wheel keeps turning.

---

## Inspirations

- **[Andrej Karpathy's `autoresearch`](https://github.com/karpathy/autoresearch)** — The original seed
- **[Sakana AI's AI Scientist](https://github.com/sakanaai/ai-scientist)** — Full-cycle automated research
- **[AutoKaggle](https://github.com/multimodal-art-projection/AutoKaggle)** — Multi-agent Kaggle orchestration
- **The Mythological Samudra Manthan** — Churning of the ocean, where effort produces both poison and nectar

---

*Built by Harsh Vardhan, 2026.*
