

raw
---
name: python-refactor
description: >
  Best practices and step-by-step guidance for refactoring Python repositories.
  Use this skill whenever the user wants to refactor, restructure, clean up, or
  modernize a Python codebase — whether the goal is improving code structure,
  performance, test coverage, or migrating to a new stack. Also trigger for
  questions about Python code quality tooling, CI setup for Python projects,
  modularization, or reducing technical debt in Python repos. Use even if the
  user only mentions one aspect (e.g. "add linting" or "how do I restructure my
  Python project").
---
 
# Python Refactoring Skill
 
A structured guide for safely and incrementally refactoring Python codebases,
including tool recommendations, team workflows, and prioritization strategies.
 
---
 
## Step 1: Audit Before Touching Anything
 
Before writing a single line, understand what you're dealing with.
 
**Measure complexity and coupling:**
```bash
pip install radon pydeps --break-system-packages
 
radon cc src/ -s          # Cyclomatic complexity — find the worst offenders
radon mi src/ -s          # Maintainability index
pydeps your_package       # Visualize module dependency graph
```
 
**Measure test coverage baseline:**
```bash
pip install pytest pytest-cov --break-system-packages
pytest --cov=src --cov-report=html
# Open htmlcov/index.html to see per-file coverage
```
 
**Key outputs from this step:**
- List of highest-complexity functions (radon cc score > 10 = priority)
- Dependency graph showing tightly coupled modules
- Current test coverage % as your baseline
---
 
## Step 2: Add Tests Before Refactoring
 
**Never refactor untested code.** The order is always:
```
existing logic → add tests → refactor
```
 
For large codebases with low coverage, prioritize:
1. **Critical business paths** — what breaks the app if wrong
2. **High-complexity functions** — from the radon output above
3. **Public interfaces** — functions/classes other modules depend on
**Recommended test stack:**
```bash
pip install pytest pytest-cov pytest-xdist --break-system-packages
```
 
```python
# pytest.ini or pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--cov=src --cov-fail-under=70"
```
 
`--cov-fail-under=70` makes CI fail if coverage drops below 70% — prevents
coverage from degrading during refactoring.
 
---
 
## Step 3: Set Up the Toolchain
 
Install and configure once, enforce forever via CI and pre-commit.
 
### Core tools
 
| Tool | Purpose | Install |
|------|---------|---------|
| `ruff` | Linting + import sorting (replaces flake8/isort) | `pip install ruff` |
| `black` | Opinionated auto-formatter | `pip install black` |
| `mypy` | Static type checking | `pip install mypy` |
| `bandit` | Security scanning | `pip install bandit` |
 
### Pre-commit (local enforcement)
 
```bash
pip install pre-commit --break-system-packages
```
 
`.pre-commit-config.yaml`:
```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.0
    hooks:
      - id: ruff
        args: [--fix]
  - repo: https://github.com/psf/black
    rev: 24.3.0
    hooks:
      - id: black
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.9.0
    hooks:
      - id: mypy
```
 
```bash
pre-commit install   # runs automatically on every commit
```
 
### CI pipeline (GitHub Actions)
 
`.github/workflows/ci.yml`:
```yaml
name: CI
on: [push, pull_request]
jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - run: pip install ruff black mypy pytest pytest-cov
      - run: ruff check .
      - run: black --check .
      - run: mypy src/
      - run: pytest --cov=src --cov-fail-under=70
```
 
---
 
## Step 4: Refactor Incrementally
 
### Rules for large/team repos
- **One concern per PR**: only restructure, OR rename, OR add types — not all at once
- **Max ~400 lines diff per PR** — larger PRs have exponentially more review friction
- **Main branch stays deployable** at all times
- **Use feature flags** to run new and old code in parallel before cutting over
### Recommended order of operations
```
1. Apply ruff + black (automated, zero-risk)      ← do this first, big win
2. Break apart high-coupling modules              ← use pydeps output as guide
3. Add type hints incrementally (mypy)            ← start with public interfaces
4. Optimize performance                           ← profile first, don't guess
5. Tech stack migration                           ← last, highest risk
```
 
### Strangler Fig Pattern (for stack migrations)
Don't replace everything at once. Instead:
```
old system keeps running
    → new module replaces one piece
        → old piece gets deleted
            → repeat until old system is gone
```
New and old code coexist; rollback is always possible.
 
---
 
## Step 5: Team Coordination
 
For multi-person repos, process matters as much as tooling.
 
**Module ownership**: Assign clear owners per module. Avoid multiple people
refactoring the same file simultaneously.
 
**Refactoring freeze periods**: Don't do large structural changes during
feature sprints or release windows.
 
**ADRs (Architecture Decision Records)**: For significant structural decisions,
write a short doc explaining what changed and why. Prevents "why did we do
this?" confusion 6 months later. Store in `docs/adr/`.
 
**PR checklist for refactor PRs:**
- [ ] Tests pass and coverage didn't drop
- [ ] No behavior changes (pure structural refactor)
- [ ] Type hints added/maintained
- [ ] CHANGELOG or ADR updated if significant
---
 
## Quick Reference: Priority Matrix
 
| Goal | Primary Tools | Risk Level |
|------|--------------|------------|
| Code style consistency | ruff, black | Very Low |
| Reduce complexity | Manual + radon guidance | Low |
| Improve test coverage | pytest, pytest-cov | Low |
| Add type safety | mypy | Medium |
| Decouple modules | rope, manual | Medium |
| Performance optimization | cProfile, line_profiler | Medium |
| Framework/stack migration | Strangler Fig pattern | High |
 
---
 
## Useful One-Liners
 
```bash
# Find all functions with complexity > 10
radon cc src/ -s --min B
 
# Check which files have no type annotations
mypy src/ --ignore-missing-imports --no-error-summary 2>&1 | grep "error:"
 
# Auto-fix all ruff issues that are safe to auto-fix
ruff check . --fix
 
# Run only tests that failed last time (fast feedback loop)
pytest --lf
 
# Run tests in parallel (4 workers)
pytest -n 4
```
