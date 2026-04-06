# Execution Guide

Chakra's execution layer decides how a version should run based on the selected strategy and runtime context.

## Strategies

| Strategy | Behavior |
|---|---|
| `local` | Runs the version locally and keeps all work on the current machine. |
| `kaggle` | Pushes the notebook workflow to Kaggle for remote execution. |
| `auto` | Chooses the best available path using the execution engine's strategy rules. |

## What `run-execution` does

1. Loads the domain manifest and execution configuration.
2. Chooses a strategy.
3. Runs the selected execution path.
4. Optionally pulls Kaggle outputs back into the repo.

Command shape:

```bash
python -m chakra --domain <domain> run-execution --version <version> [options]
```

Common options:

- `--strategy {local,kaggle,auto}`
- `--pull-outputs`
- `--dry-run`
- `--username`
- `--title`

## Local execution

Local execution is the default when you want to keep everything on the same machine. It is the best choice for:

- quick iteration
- debugging runner behavior
- deterministic smoke checks

Example:

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy local
```

## Kaggle execution

Kaggle is useful when the domain is designed to run as a notebook workflow or when you want to use a managed remote environment.

The docs and the CLI keep the Kaggle path explicit so you can see when outputs are pushed or pulled.

Example:

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy kaggle --pull-outputs
```

## Automatic selection

Use `--strategy auto` when you want Chakra to choose the route. The execution engine considers the manifest, the runtime environment, and the local smoke gate before it commits to a remote run.

Example:

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto
```

## Smoke gate

Chakra's lifecycle includes a local smoke gate before heavier remote work is attempted. That keeps the system from pushing an obviously broken version to Kaggle.

If smoke fails, Chakra blocks the remote path and returns an execution error.

## Dry-run behavior

Use `--dry-run` to preview execution decisions and commands without mutating remote state.

```bash
python -m chakra --domain tabular_cls run-execution --version v1.0 --strategy auto --dry-run
```

This is the safest way to verify strategy and routing before a real run.