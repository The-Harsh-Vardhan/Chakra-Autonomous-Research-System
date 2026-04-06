# Vision Audit — Current State vs Long-Term Aim

**Date:** 2026-04-05  
**Scope:** Full system audit against the ultimate autonomous research vision

---

## The Ultimate Vision

Chakra's long-term aim is to be a **fully autonomous research system** that:

1. **Plans** experiments based on prior results (Sutra)
2. **Executes** training runs without human intervention (Yantra)
3. **Guards** against invalid or broken results automatically (Rakshak)
4. **Reviews** results and generates human-readable reports (Vimarsh)
5. **Improves** by proposing and implementing the next experiment (Manthan)
6. **Cycles** indefinitely, producing publication-ready research overnight (Aavart)

The end state: *you start an Aavart before bed, wake up to a better model and a written report explaining why.*

---

## Grading Scale

| Grade | Meaning |
|-------|---------|
| ✅ **A** | Feature complete and battle-tested |
| ⚠️ **B** | Works but has gaps or hasn't been stress-tested |
| 🟡 **C** | Exists structurally but incomplete or never run end-to-end |
| 🔴 **D** | Conceptually planned but not implemented |
| ⬛ **F** | Not started, not even scaffolded |

---

## 1. Core Architecture

| Component | Current State | Grade | Gap |
|-----------|--------------|-------|-----|
| `DomainLifecycleHooks` Protocol | 14-method protocol, `@runtime_checkable`, used by 3 domains | ✅ A | None — clean abstraction |
| Domain auto-discovery | `domain.yaml` scanning, structured manifests | ✅ A | None |
| Config inheritance | `inherits:` key, deep merge, 3-variant pattern | ✅ A | None |
| W&B tracker + NullTracker | Graceful fallback, automatic credential loading | ✅ A | None |
| Path utilities / seeding | Works, deterministic seeds, dotenv loading | ⚠️ B | `get_device()` lacks try/except for missing torch |
| CLI (traditional) | `python -m chakra` with domain dispatch | ✅ A | None |
| CLI (Chakra) | `chakra sutra/yantra/rakshak/vimarsh/manthan/aavart` | ⚠️ B | Built but `aavart` hasn't completed a full end-to-end run |
| ChakraLogger | Structured stage-aware logging with emoji | ✅ A | None — clean and focused |

**Architecture Grade: A-**  
The foundation is solid. The protocol pattern, config system, and tracker are production-quality.

---

## 2. Domain Maturity

### 2.1 Tabular Classification (`tabular_cls`)

| Feature | State | Grade |
|---------|-------|-------|
| Models (Logistic, MLP) | Working, tested | ✅ A |
| Dataset (Iris, Titanic) | Working, tested | ✅ A |
| Metrics (Accuracy, F1) | Working, tested | ✅ A |
| Train runner | Working, W&B integrated | ✅ A |
| Evaluate runner | Working, checkpoint loading | ✅ A |
| Lifecycle hooks (all 14) | Complete | ✅ A |
| Configs (base + v1.0 + v2.0) | Complete | ✅ A |
| Tests (9) | All passing | ✅ A |
| Benchmark registry | Real measured baselines | ✅ A |

**Domain Grade: A** — This is the reference implementation. Clean, tested, works end-to-end.

### 2.2 NLP Language Modelling (`nlp_lm`)

| Feature | State | Grade |
|---------|-------|-------|
| Models (GPT-nano, Bigram) | Working, tested | ⚠️ B |
| Dataset (Tiny Shakespeare) | Working | ⚠️ B |
| Metrics (BPB, Perplexity) | Working, tested | ✅ A |
| Train runner | Present but has config path issues | 🟡 C |
| Evaluate runner | Present but never fully validated | 🟡 C |
| Lifecycle hooks | 14 methods implemented, but `render_notebook()` generates near-empty notebook | 🟡 C |
| Configs | Complete | ✅ A |
| Tests (6) | Pass | ⚠️ B |
| Benchmark registry | Has entries | ⚠️ B |

**Domain Grade: C+** — The weakest domain. Models and metrics work, but the notebook is empty, and the runners haven't been validated end-to-end. This is the first candidate for repair.

### 2.3 HNDSR Super-Resolution (`hndsr_vr`)

| Feature | State | Grade |
|---------|-------|-------|
| Models (SR3 diffusion, Bicubic) | Present, ported from working code | ⚠️ B |
| Dataset (Satellite imagery) | Requires local data | ⚠️ B |
| Metrics (PSNR, SSIM) | Present | ⚠️ B |
| Train/Evaluate runners | Present, structurally correct | ⚠️ B |
| Lifecycle hooks (all 14) | Complete, mature (400+ lines) | ✅ A |
| Configs | Complete | ✅ A |
| Tests | Skip without torch | ⚠️ B |
| Notebook contract | Validated, the only domain with contract tests | ✅ A |

**Domain Grade: B+** — Mature codebase ported from working original. Can't verify without GPU + dataset, but structurally the strongest lifecycle implementation.

---

## 3. Cycle Completeness (Aavart)

This is the most critical audit dimension: **can Chakra complete a full cycle?**

| Cycle Stage | Chakra Name | CLI | Implementation | End-to-End Tested? | Grade |
|-------------|-------------|-----|----------------|-------------------|-------|
| Plan | Sutra | `chakra sutra` | `scaffold_version()` | ✅ Verified | ✅ A |
| Execute (Control) | Yantra | `chakra yantra --stage control` | Subprocess to `train_runner` | ✅ Verified for tabular_cls | ⚠️ B |
| Execute (Smoke) | Yantra | `chakra yantra --stage smoke` | Subprocess to `train_runner` | ✅ Verified for tabular_cls | ⚠️ B |
| Execute (Train) | Yantra | `chakra yantra --stage train` | Subprocess to `train_runner` | ✅ Verified for tabular_cls | ⚠️ B |
| Execute (Eval) | Yantra | `chakra yantra --stage eval` | Subprocess to `evaluate_runner` | ✅ Verified for tabular_cls | ⚠️ B |
| Guard | Rakshak | `chakra rakshak` | `validate_version()` | ✅ Verified | ✅ A |
| Review | Vimarsh | `chakra vinarsh` | `sync_run()` + `review_run()` | ✅ Verified | ⚠️ B |
| Improve | Manthan | `chakra manthan` | `next_ablation()` | ⚠️ Runs but outputs are generic | 🟡 C |
| Full Cycle | Aavart | `chakra aavart` | Orchestrates all stages | 🔴 **Never completed end-to-end** | 🔴 D |

**Cycle Grade: C**

> [!WARNING]
> **The `chakra aavart` command has never completed a full end-to-end run.** Individual stages work in isolation, but the complete orchestrated cycle (Sutra → Yantra × 4 → Vimarsh → Rakshak → Manthan) has not been executed as a single command. This is the single most important gap to close.

---

## 4. Autonomy Level

| Level | Description | Current State | Grade |
|-------|-------------|---------------|-------|
| **Level 0** | Fully manual | ~~Human runs every command~~ | Passed |
| **Level 1** | Structured manual | Human follows the lifecycle in order | ✅ Current state |
| **Level 2** | One-command cycle | `chakra aavart` runs everything | 🟡 Built but untested |
| **Level 3** | Semi-autonomous | LLM reads Manthan output, generates next Sutra | 🔴 Not started |
| **Level 4** | Autonomous looping | System chains Aavarts without human input | ⬛ Not started |
| **Level 5** | Self-improving | System modifies its own training code | ⬛ Not started |

**Autonomy Grade: Level 1 (approaching Level 2)**

---

## 5. Observability & Tracking

| Feature | State | Grade |
|---------|-------|-------|
| W&B metric streaming | Working for all domains | ✅ A |
| Local JSON fallback (NullTracker) | Working | ✅ A |
| Structured reviews with findings | Working | ⚠️ B |
| Benchmark registry (delta tracking) | Working for tabular_cls + nlp_lm | ⚠️ B |
| ChakraLogger (stage-aware output) | Working | ✅ A |
| Run manifests (`run_manifest.json`) | Generated by `sync_run` | ⚠️ B |
| Web dashboard | Not implemented | ⬛ F |
| Multi-seed statistical significance | Not implemented | ⬛ F |
| Ablation lineage visualization | Not implemented | ⬛ F |

**Observability Grade: B**

---

## 6. Testing & CI

| Feature | State | Grade |
|---------|-------|-------|
| Test suite | 47 tests across 8 files | ✅ A |
| CI pipeline | GitHub Actions `test.yml` | ✅ A |
| Core-only tests (no torch) | test_core, test_domain_registry, test_cli_dispatch | ✅ A |
| Domain-specific tests | Require torch, skip gracefully | ⚠️ B |
| Integration tests (full Aavart) | **None** | 🔴 D |
| Performance benchmarks | None | ⬛ F |

**Testing Grade: B+**

---

## 7. Documentation

| Document | State | Grade |
|----------|-------|-------|
| README.md | Chakra-branded showcase with cycle diagram | ✅ A |
| JOURNEY.md | Project evolution narrative | ✅ A (just created) |
| docs/how_to_use.md | 800-line comprehensive guide | ⚠️ B (some stale branding) |
| docs/quickstart.md | 5-minute walkthrough | ⚠️ B (stale branding) |
| docs/chakra_cycle.md | Deep-dive into each stage | ✅ A |
| CONTRIBUTING.md | Domain extension tutorial | ⚠️ B (stale branding) |
| Research program docs | `programs/*.md` for each domain | ⚠️ B |

**Documentation Grade: B+**

---

## 8. Summary Scorecard

| Dimension | Grade | Priority to Fix |
|-----------|-------|----------------|
| **Core Architecture** | A- | Low — solid foundation |
| **Tabular Domain** | A | None — reference quality |
| **NLP Domain** | C+ | HIGH — repair runners + notebook |
| **HNDSR Domain** | B+ | Medium — needs runtime validation |
| **Cycle Completeness** | C | **CRITICAL — run first Aavart** |
| **Autonomy Level** | Level 1 | Medium — Level 2 is one test away |
| **Observability** | B | Medium — dashboard would be transformative |
| **Testing & CI** | B+ | Medium — add Aavart integration test |
| **Documentation** | B+ | Low — branding updates in progress |

---

## 9. Priority Roadmap

### 🔴 Critical (This Week)
1. **Run the first complete Aavart** — `chakra aavart --domain tabular_cls --version v1.0 --device cpu --force`. This is the single most important milestone. Until this succeeds, Chakra is a collection of parts, not a system.
2. **Fix NLP train/evaluate runners** — Config path issues prevent the NLP domain from completing its cycle.

### 🟡 Important (This Month)
3. **NLP notebook** — Add actual training/evaluation subprocess cells so the notebook is Kaggle-ready.
4. **Integration test** — Add a test that runs `chakra aavart` on tabular_cls and asserts success.
5. **Multi-seed runs** — Add `--seeds 3` flag to Aavart for statistical confidence.
6. **Manthan improvements** — Make ablation suggestions context-aware based on actual eval results, not hardcoded strings.

### 🟢 Nice-to-Have (Next Quarter)
7. **Web dashboard** — Visualize cycle history, metric trends, ablation lineage.
8. **Agent integration** — LLM reads Manthan output and generates next Sutra automatically.
9. **Cross-domain insights** — Patterns that improve one domain are suggested for others.
10. **Paper generation** — Auto-generate LaTeX research reports from review findings.

---

## 10. The Honest Assessment

Chakra is an **impressive architectural achievement for a solo developer**. The Protocol pattern, config system, W&B integration, and Chakra identity layer are genuinely well-engineered.

But it hasn't proven its thesis yet. The system claims to be a "cyclic autonomous research engine" — but no cycle has completed. Individual stages work. The architecture is sound. The CLI is ready. But the wheel hasn't actually turned.

**The single action that would change everything:** Run `chakra aavart --domain tabular_cls --version v1.0 --device cpu --force` and watch it succeed from Sutra to Manthan. 

That one command transforms Chakra from "a collection of well-designed parts" to "a working autonomous research system."

Then do it again with v1.1. That proves the cycle.
