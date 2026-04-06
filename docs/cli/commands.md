# Command Reference

This page documents both command surfaces:

- `python -m chakra` (full lifecycle + execution + Kaggle utility surface)
- `chakra` (Chakra-branded stage aliases)

## Global argument pattern

Most `python -m chakra` commands require a domain.

```bash
python -m chakra --domain <domain_name> <command> [options]
```

Commands that do not require `--domain`:

- `list-domains`

## Discovery commands

### `python -m chakra list-domains`

Lists discovered domains in a table.

```bash
python -m chakra list-domains
```

Representative output:

```text
Name                 Display Name                             Primary Metric
--------------------------------------------------------------------------------
hndsr_vr             HNDSR Satellite Super-Resolution         psnr_mean
nlp_lm               NLP Language Modelling                   val_bpb
tabular_cls          Tabular Classification                   accuracy
```

### `python -m chakra domain-info`

Shows manifest details for one domain.

Arguments:

- `--name` optional if `--domain` is already set

Examples:

```bash
python -m chakra domain-info --name tabular_cls
python -m chakra --domain tabular_cls domain-info
```

Representative output:

```text
Name:             tabular_cls
Display Name:     Tabular Classification
Version Pattern:  ^v\d+\.\d+$
Model Kinds:      logistic_regression, mlp
Primary Metric:   accuracy (max)
```

## Planning commands

### `python -m chakra scaffold-version`

Creates notebook/doc/review/config assets for a version.

Arguments:

- `--version` required
- `--parent` optional
- `--lineage {scratch,pretrained}` optional, default `scratch`
- `--force` optional

Example:

```bash
python -m chakra --domain tabular_cls scaffold-version --version v1.0 --force
```

### `chakra sutra`

Chakra alias of the planning stage.

```bash
chakra sutra --domain tabular_cls --version v1.0 --force
```

## Execution commands

### `python -m chakra run-execution`

Runs orchestration using local/Kaggle/auto strategy.

Arguments:

- `--version` required
- `--strategy {local,kaggle,auto}` optional, default `auto`
- `--title` optional (Kaggle title override)
- `--username` optional (Kaggle user override)
- `--pull-outputs` optional
- `--dry-run` optional

Examples:

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy local
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto --pull-outputs
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy kaggle --dry-run
```

Representative output:

```text
Resolved train config: .../configs/tabular_cls/v1.0_train.yaml
Requested execution strategy: auto
Execution decision: local (system profile prefers local execution)
```

### `chakra yantra`

Runs one execution stage directly.

Arguments:

- `--version` required
- `--stage {control,smoke,train,eval}` required
- `--device` optional, default `cpu`

Examples:

```bash
chakra yantra --domain tabular_cls --version v1.0 --stage train --device cpu
chakra yantra --domain tabular_cls --version v1.0 --stage eval --device cpu
```

## Kaggle utility commands

### `python -m chakra push-kaggle`

Prepares kernel metadata and pushes notebook bundle.

Arguments:

- `--version` required
- `--title` optional
- `--username` optional
- `--dry-run` optional

### `python -m chakra kaggle-status`

Checks Kaggle kernel status.

Arguments:

- `--version` required
- `--username` optional
- `--dry-run` optional

### `python -m chakra pull-kaggle`

Pulls Kaggle outputs into artifacts.

Arguments:

- `--version` required
- `--username` optional
- `--dry-run` optional

## Sync, review, and improvement commands

### `python -m chakra sync-run`

Indexes a run directory into run manifest data.

Arguments:

- `--version` required
- `--source-dir` optional
- `--wandb-url` optional
- `--dry-run` optional

### `python -m chakra review-run`

Generates review output for a version.

Arguments:

- `--version` required

### `python -m chakra next-ablation`

Writes bounded follow-up suggestions.

Arguments:

- `--version` required

### `python -m chakra mirror-obsidian`

Writes or previews an Obsidian mirror note for a run.

Arguments:

- `--version` required
- `--output-dir` optional
- `--dry-run` optional

## Validation command

### `python -m chakra validate-version`

Validates version contract completeness.

Arguments:

- `--version` required

Representative success output:

```text
v1.0 contract passed for domain 'tabular_cls'.
```

## Chakra lifecycle aliases

| Alias | Stage | Description |
|---|---|---|
| `chakra sutra` | Plan | Scaffold and freeze assets |
| `chakra yantra` | Execute | Run a selected execution stage |
| `chakra rakshak` | Guard | Validate version contract |
| `chakra vimarsh` | Review | Sync and generate review |
| `chakra manthan` | Improve | Generate ablation suggestions |
| `chakra aavart` | Full cycle | Run full Sutra→Yantra→Rakshak→Vimarsh→Manthan loop |