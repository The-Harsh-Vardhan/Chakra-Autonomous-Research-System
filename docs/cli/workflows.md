# Workflow Recipes

This page gives practical command sequences for common operator tasks.

## 1. First run from scratch

```bash
python -m chakra list-domains
python -m chakra --domain tabular_cls scaffold-version --version v1.0 --force
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy local
python -m chakra --domain tabular_cls validate-version --version v1.0
python -m chakra --domain tabular_cls review-run --version v1.0
python -m chakra --domain tabular_cls next-ablation --version v1.0
```

Use this path when you want a clear, local-first baseline.

## 2. Full lifecycle via Chakra aliases

```bash
chakra aavart --domain tabular_cls --version v1.0 --device cpu --force
```

This runs full cycle orchestration in one command.

## 3. Stage-by-stage debug workflow

```bash
chakra sutra --domain tabular_cls --version v1.0 --force
chakra yantra --domain tabular_cls --version v1.0 --stage control --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage smoke --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage train --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage eval --device cpu
chakra rakshak --domain tabular_cls --version v1.0
chakra vimarsh --domain tabular_cls --version v1.0
chakra manthan --domain tabular_cls --version v1.0
```

Use this path when you need per-stage visibility.

## 4. Kaggle-assisted execution

```bash
python -m chakra --domain tabular_cls push-kaggle --version v1.0
python -m chakra --domain tabular_cls kaggle-status --version v1.0
python -m chakra --domain tabular_cls pull-kaggle --version v1.0
python -m chakra --domain tabular_cls sync-run --version v1.0
python -m chakra --domain tabular_cls review-run --version v1.0
```

Use this path when the domain run is notebook-centered.

## 5. Safe preview before remote run

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto --dry-run
```

This previews routing decisions without committing remote side effects.

## 6. Obsidian mirror generation

```bash
python -m chakra --domain tabular_cls mirror-obsidian --version v1.0 --output-dir reports/generated
```

Use this when you want run notes in second-brain format.