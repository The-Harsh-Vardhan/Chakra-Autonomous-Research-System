# CLI Cheatsheet

Quick command lookup for daily Chakra usage.

## Discovery

```bash
python -m chakra list-domains
python -m chakra domain-info --name tabular_cls
```

## Plan and validate

```bash
python -m chakra --domain tabular_cls scaffold-version --version v1.0 --force
python -m chakra --domain tabular_cls validate-version --version v1.0
```

## Execution (single command)

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy local
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto --pull-outputs
```

## Execution (stage-wise)

```bash
chakra yantra --domain tabular_cls --version v1.0 --stage control --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage smoke --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage train --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage eval --device cpu
```

## Review and improve

```bash
python -m chakra --domain tabular_cls sync-run --version v1.0
python -m chakra --domain tabular_cls review-run --version v1.0
python -m chakra --domain tabular_cls next-ablation --version v1.0
```

## Full cycle

```bash
chakra aavart --domain tabular_cls --version v1.0 --device cpu --force
```

## Kaggle utility

```bash
python -m chakra --domain tabular_cls push-kaggle --version v1.0
python -m chakra --domain tabular_cls kaggle-status --version v1.0
python -m chakra --domain tabular_cls pull-kaggle --version v1.0
```

## Safe preview mode

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto --dry-run
```