# python-learning-fundamentals

A structured Python learning curriculum organised by topic and progressive difficulty. Each module contains annotated Jupyter notebooks, worked exercises, and runnable examples. This repository supersedes `MacOS_Repository_Restructured` (legacy account: `amcm_dev01`).

---

## Repository Status

| Item | Detail |
|---|---|
| Language | Python 3.11 |
| Runtime | JupyterLab 4.x |
| Environment | Conda · `environment.yml` · `py-fundamentals` |
| Kernel | `py-fundamentals` (registered via `ipykernel`) |
| Output Stripping | `nbstripout` git hook — strips cell outputs on every commit |
| Legacy Repo | `amcm_dev01/MacOS_Repository_Restructured` — archived, read-only |
| Owner | AMCMDV |

---

## Prerequisites

| Requirement | Version | Purpose |
|---|---|---|
| [Miniconda](https://docs.conda.io/en/latest/miniconda.html) or [Anaconda](https://www.anaconda.com/) | ≥ 23.x | Environment and package management |
| Git | ≥ 2.39 | Version control |
| macOS / Linux | — | Windows requires WSL2 for full shell compatibility |

---

## Setup

```bash
# 1. Clone the repository
git clone https://github.com/<saehl-org>/python-learning-fundamentals.git
cd python-learning-fundamentals

# 2. Create and activate the Conda environment
conda env create -f environment.yml
conda activate py-fundamentals

# 3. Register the Jupyter kernel
python -m ipykernel install --user \
  --name py-fundamentals \
  --display-name "Python 3.11 (py-fundamentals)"

# 4. Install the nbstripout git hook
#    Strips cell outputs automatically on every commit — required, not optional
nbstripout --install
nbstripout --status   # verify: "nbstripout is installed in this repository"

# 5. Launch JupyterLab
jupyter lab
```

> **Note for contributors**: Steps 3 and 4 must be completed in every fresh clone. Skipping step 4 will cause cell outputs containing local paths to enter git history permanently.

---

## Learning Path

Work through modules sequentially. Each module directory contains a `README.md` with learning objectives and a completion checklist. Do not skip modules — each introduces concepts used by the next.

```
01-basics/                  → Data types, file I/O, sets, dictionaries
02-control-flow/            → Loops, conditionals, then list comprehensions
03-functions/               → Definitions, scope, *args/**kwargs, exercises
04-error-handling/          → try/except/else/finally, explicit exception types
05-oop/                     → Classes, inheritance, polymorphism, dunder protocol
06-modules-and-packages/    → Import system, packages, pip, PyPI
07-projects/
  ├── project-01-confidence-game/   → Milestone 1: CLI game (functions + I/O)
  └── project-02-card-game/         → Milestone 2: Card game (OOP full stack)
scratch/                    → Experimental — not reviewed, not in CI
```

---

## Module Summary

| Module | Key Concepts | Notebooks |
|---|---|---|
| `01-basics` | File I/O, context managers, sets, dictionaries | 3 |
| `02-control-flow` | `for`/`while`, `break`/`continue`, `enumerate()`, comprehensions | 2 |
| `03-functions` | Definitions, scope, `*args`/`**kwargs`, exercises (levels 1 & 2) | 5 |
| `04-error-handling` | `try`/`except`/`else`/`finally`, explicit exception types | 2 |
| `05-oop` | Classes, inheritance, `super()`, polymorphism, dunder methods | 3 |
| `06-modules-and-packages` | Import system, `__init__.py`, pip, PyPI, PEP 517 build | 2 + 3 supporting files |
| `07-projects` | End-to-end CLI programs applying all prior modules | 3 |

---

## Documentation Index

| Document | Purpose |
|---|---|
| `ARCHITECTURE.md` | Design decisions (6-column matrix), full module breakdown, all bug fix details, migration phases, risk assessment |
| `MIGRATION.md` | Step-by-step operational runbook — from legacy clone cleanup to GitHub archival |
| `BACKLOG.md` | Live structured register: 42 items across BUG / STR / MOD / GAP / DEF / CI / GOV / ENV categories |

---

## Licence

Private. All rights reserved. SAEHL © 2025.
