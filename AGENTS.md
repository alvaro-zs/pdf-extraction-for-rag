# Agent instructions

## Python environment

Use the repository's local virtual environment for package installation, scripts, tests, and benchmarks. From the repository root, invoke `.\.venv\Scripts\python.exe` explicitly rather than relying on shell activation. Install packages with `.\.venv\Scripts\python.exe -m pip`.

If the environment is missing or unusable, stop Python-dependent work and report the problem before proceeding. Do not fall back to global Python.

## Agent skills

### Issue tracker

Before reading, creating, or updating tickets, read [the GitHub issue tracker conventions](docs/agents/issue-tracker.md).

### Triage labels

Before triaging tickets or applying triage labels, read [the five-role label mapping](docs/agents/triage-labels.md).

### Domain docs

Before exploring the codebase or proposing domain changes, read [the single-context domain documentation rules](docs/agents/domain.md).
