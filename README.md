# python-learning-fundamentals

A structured Python learning repository organised by topic and progression level. Each module contains annotated Jupyter notebooks, worked exercises, and small runnable examples. This repository supersedes `MacOS_Repository_Restructured` on the legacy `amcm_dev01` account.

---

## Repository Status

| Item | Value |
|---|---|
| Language | Python 3.11+ |
| Runtime | Jupyter Lab / Notebook |
| Environment Manager | Conda (`environment.yml`) |
| Legacy Repo | `amcm_dev01/MacOS_Repository_Restructured` (deprecated) |
| Owner | AMCMDV |

---

## Prerequisites

- [Miniconda](https://docs.conda.io/en/latest/miniconda.html) or [Anaconda](https://www.anaconda.com/)
- Git ≥ 2.39

---

## Setup

```bash
# 1. Clone the repository
git clone https://github.com/<new-org>/python-learning-fundamentals.git
cd python-learning-fundamentals

# 2. Create and activate the Conda environment
conda env create -f environment.yml
conda activate py-fundamentals

# 3. Launch Jupyter Lab
jupyter lab
```

---

## Learning Path

Work through modules sequentially. Each module directory contains a `README.md` with objectives and a checklist.

```
01-basics/                  → Data types, I/O, sets, dictionaries
02-control-flow/            → Loops, conditionals, list comprehensions
03-functions/               → Functions, *args/**kwargs, scope, exercises
04-error-handling/          → try/except, finally, custom exceptions
05-oop/                     → Classes, inheritance, polymorphism, dunder methods
06-modules-and-packages/    → Import system, pip, PyPI, writing packages
07-projects/
  ├── project-01-confidence-game/   → Milestone 1: CLI game (functions + I/O)
  └── project-02-card-game/         → Milestone 2: Card game (OOP full stack)
scratch/                    → Personal sandpit — not reviewed or tested
```

---

## Module Summary

| Module | Key Concepts | Notebooks |
|---|---|---|
| `01-basics` | Input/output, file I/O, sets, dicts | 3 |
| `02-control-flow` | Comprehensions, loops, conditionals | 1 |
| `03-functions` | Definitions, scope, *args/**kwargs, exercises | 5 |
| `04-error-handling` | try/except/finally, IOError, ZeroDivisionError | 2 |
| `05-oop` | Classes, inheritance, polymorphism, `__str__`, `__del__` | 3 |
| `06-modules-and-packages` | Import system, `__init__.py`, pip, PyPI | 2 + 2 `.py` files |
| `07-projects` | End-to-end CLI programs | 3 |

---

## Repository Structure

```
python-learning-fundamentals/
├── .gitignore
├── README.md               ← You are here
├── ARCHITECTURE.md
├── MIGRATION.md
├── environment.yml
├── 01-basics/
│   ├── README.md
│   ├── input_output.ipynb
│   ├── sets.ipynb
│   └── dictionaries.ipynb
├── 02-control-flow/
│   ├── README.md
│   └── list_comprehensions.ipynb
├── 03-functions/
│   ├── README.md
│   ├── methods_and_documentation.ipynb
│   ├── function_interactions.ipynb
│   ├── args_kwargs.ipynb
│   ├── exercises_level_one.ipynb
│   └── exercises_level_two.ipynb
├── 04-error-handling/
│   ├── README.md
│   ├── errors_and_exceptions.ipynb
│   └── exercises.ipynb
├── 05-oop/
│   ├── README.md
│   ├── oop_fundamentals.ipynb
│   ├── oop_advanced.ipynb
│   └── special_methods.ipynb
├── 06-modules-and-packages/
│   ├── README.md
│   ├── modules_and_packages.ipynb
│   ├── pip_and_pypi.ipynb
│   └── examples/
│       ├── drinks.py
│       └── demo.py
├── 07-projects/
│   ├── README.md
│   ├── project-01-confidence-game/
│   │   ├── README.md
│   │   ├── warmup_exercises.ipynb
│   │   └── confidence_game.ipynb
│   └── project-02-card-game/
│       ├── README.md
│       └── bicycle_war.ipynb
└── scratch/
    └── .gitkeep
```

---

## Known Code Issues (Resolved in Migration)

The following bugs existed in the legacy repository and are corrected in this version:

| File (Legacy) | Issue | Fix Applied |
|---|---|---|
| `.ipynb_checkpoints/Error-checkpoint.ipynb` | `i = input(...)` used raw string in `while i > 0` — `TypeError` on comparison | Cast to `int`: `i = int(input(...))` |
| `OOP Extended Version.ipynb` | `Stadium = Music('Stadium')` — `Music` class undefined; class is named `Sound` | Renamed instantiation to `Stadium = Sound('Stadium')` |
| `[Sandpit] A Confidence Game` | `return int(guess)` inside `while` loop — exits before validation loop completes | Dedented `return` to execute after loop exits |
| `exercises_level_one.ipynb` | `animaCrackers` defined, called as `animalCrackers` — `NameError` | Unified to `animal_crackers` (snake_case, consistent) |

---

## .gitignore

The following are excluded from version control (missing in the legacy repo):

```gitignore
# Jupyter
.ipynb_checkpoints/
*.pyc
__pycache__/

# Conda / pip
*.egg-info/
dist/
build/
.env
.venv

# OS
.DS_Store
Thumbs.db
```

---

## Environment Specification

```yaml
# environment.yml
name: py-fundamentals
channels:
  - defaults
  - conda-forge
dependencies:
  - python=3.11
  - jupyter
  - jupyterlab
  - ipykernel
  - requests
  - beautifulsoup4
  - pip
```

---

## Contributing

This is a personal learning repository maintained by SAEHL. No external contributions are expected. If you are forking this for your own use, please remove the AMCMDV branding.

---

## Licence

Private. All rights reserved. AMCMDV © 2025.