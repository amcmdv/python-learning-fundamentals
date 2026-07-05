# ARCHITECTURE.md
<!-- python-learning-fundamentals -->
<!-- Last revised: 2025 · Supersedes: MacOS_Repository_Restructured (amcm_dev01) -->

---

## System Overview

This repository is a sequenced Python learning curriculum stored as annotated Jupyter notebooks. It is not a deployable application — it is a structured knowledge base with executable examples, exercises, and milestone projects.

The architecture principle is **progressive disclosure**: each module unlocks concepts required by the next. Modules are numbered to enforce this ordering. Within each module, notebooks are named in `snake_case` by concept, not by section number or legacy prefix.

---

## Assumptions

The following constraints are in effect for this migration. Changes to any of these require a corresponding update to the affected phase.

- **Runtime environment**: Conda (conda-forge channel), single developer workstation, macOS. No cloud execution target for this migration cycle.
- **Python target**: 3.11 LTS (EOL October 2027). Python 3.8.3 is EOL and constitutes a security liability — no assumption of backward compatibility.
- **Scale**: Single maintainer. No concurrent notebook editing. CI/CD is additive (Phase 4), not a migration blocker.
- **Budget**: Zero cost. All tooling is open-source.
- **Compliance**: No PII, no secrets, no externally accessible services. `.ipynb` output cells are treated as a potential data leakage vector (embedded paths) and are stripped at the git layer.

---

## Design Decisions

| Technology / Approach | Initial Cost | Maintenance Overhead | Security Risk | Scale Limit | Verdict |
|---|---|---|---|---|---|
| **Jupyter Notebooks (.ipynb)** vs pure `.py` scripts | Low — content already in `.ipynb` format | Medium — kernel management; noisy git diffs without output stripping | Low — static content; no auth surface. Risk: output cells embed user paths. Mitigated by `nbstripout`. | ~180 notebooks before `Restart & Run All` becomes untenable. Mitigated by `pytest --nbmake`. | **Retain** — interleaved markdown/code is the correct format for annotated learning content |
| **Numbered directory prefix** (`01-`, `02-`) vs alphabetical vs flat | Low — directory rename only | Low — ordering is self-documenting | None | 2-digit padding fails at 100+ modules. Not a realistic constraint. | **Adopt** — enforces progressive disclosure; prevents arbitrary navigation |
| **`snake_case` filenames** vs legacy `[bracket]` prefixes with spaces | Low — batch rename | Low — consistent rule with no exceptions | Low — spaces and brackets in filenames cause shell injection risk if paths are ever concatenated dynamically | None | **Adopt** — eliminates shell-escaping requirement. Convention is consistent with Unix filesystem and Python module naming norms, not PEP 8 (which governs source identifiers, not `.ipynb` filenames) |
| **`conda environment.yml`** vs `pip requirements.txt` | Low — file authored | Low — single file pins Python version and packages together | Low — conda-forge packages are community-reviewed | Conda resolution slows at >100 packages. Not applicable here. | **Adopt** — Conda is the existing toolchain; `environment.yml` is the correct artifact. Maintain a `requirements.txt` as a pip fallback. |
| **Python 3.11 LTS** vs Python 3.8.3 (legacy) | Medium — requires re-running all notebooks and updating kernel metadata | Low — 3.11 supported until October 2027 | **High to retain 3.8** — EOL October 2024; no security patches issued for any 3.8.x CVEs post-EOL | N/A | **Upgrade to 3.11** — 3.8.3 retention is a security liability with no mitigation path |
| **`nbstripout` git hook** vs manual `jupyter nbconvert --clear-output` | Low — single install command | Very Low — runs automatically on every commit | Manual: **High** — one missed run permanently embeds user paths in git history. Hook: Low. | None | **Adopt `nbstripout`** — automated hook eliminates the failure class entirely; manual approach is not reliable at scale |
| **Dedicated `scratch/` directory** vs inline `[Sandpit]`-prefixed notebooks in curriculum | Low — directory creation + `.gitkeep` | Low — with enforced lifecycle policy (see Governance) | None | Unbounded accumulation without lifecycle policy | **Adopt `scratch/`** — enforces a hard boundary between reviewed curriculum and unreviewed experimental content |

---

## Module Breakdown

### `01-basics/`

**Objective**: Establish fluency with Python's core data types, I/O primitives, and built-in collection operations.

| File | Legacy Source | Concepts |
|---|---|---|
| `input_output.ipynb` | `[Notes] Basic Input Output.ipynb` | `open()`, `read()`, `readlines()`, `write()`, context managers (`with`) |
| `sets.ipynb` | `[Notes] Sets.ipynb` | Set creation, membership, deduplication, `set()` on iterables |
| `dictionaries.ipynb` | `Dictionaries in Python-checkpoint.ipynb` ¹ | Key-value pairs, nesting, `.get()`, `.items()`, `.values()` |

¹ No canonical `.ipynb` counterpart exists outside `.ipynb_checkpoints/`. Treat the checkpoint as source, verify `my_dictionary1['key2']` returns the list `[0, 1, 2, 3, 4]` and not the string `'[0,1,2,3,4]'` — a corrupted output cell was observed in the legacy version.

**Modernisation Actions**:
- `input_output.ipynb`: Replace all bare `open()` calls with `with open(...) as f:` context managers. The legacy notebook opens `myfile = open(...)` without a corresponding `myfile.close()`, leaking file handles.

---

### `02-control-flow/`

**Objective**: Cover looping constructs, conditional logic, and Pythonic iteration patterns.

| File | Legacy Source | Concepts |
|---|---|---|
| `loops_and_conditionals.ipynb` | **New file — in scope for Phase 2** | `for`, `while`, `break`, `continue`, `enumerate()`, `zip()`, `if`/`elif`/`else`, ternary expressions |
| `list_comprehensions.ipynb` | `[Notes] List Comprehensions.ipynb` | List comprehensions, conditional comprehensions, nested comprehensions |

**Decision**: `loops_and_conditionals.ipynb` is a content gap identified during migration analysis. It is **in scope for this migration** and will be created as a complete notebook in Phase 2. Without it, `list_comprehensions.ipynb` has no prerequisite foundation — a learner encounters comprehension syntax before seeing basic loop syntax. Tracked in `BACKLOG.md` as `GAP-01`.

---

### `03-functions/`

**Objective**: Define, call, and compose functions. Understand scope, return values, and variadic signatures.

| File | Legacy Source | Concepts |
|---|---|---|
| `methods_and_documentation.ipynb` | `[Notes] Methods and The Python Documentation.ipynb` | Built-in methods, `dir()`, `help()`, list methods |
| `function_interactions.ipynb` | `[Notes] Interactions Between Python Functions.ipynb` | Functional decomposition, calling functions from functions, `shuffle` |
| `args_kwargs.ipynb` | `[Notes] More *Args & **Kwargs.ipynb` | `*args`, `**kwargs`, wrapper functions |
| `exercises_level_one.ipynb` | `[Section 6] Function Practice Exercises {Code Along}.ipynb` | `animal_crackers`, `makes_twenty`, mixed logic exercises |
| `exercises_level_two.ipynb` | `[Section 6] Function Practice Exercises - Level Two.ipynb` | `blackjack`, `summer_of_69`, advanced logic exercises |

**Bug Fixes Required**:

```python
# BUG-04 — exercises_level_one.ipynb
# Two different spellings used across cells — NameError on call
def animaCrackers(text): ...        # defined (camelCase, typo)
animalCrackers('Levelheaded Llama') # called — NameError

# FIX: Standardise to snake_case
def animal_crackers(text):
    word_list = text.lower().split()
    return word_list[0][0] == word_list[1][0]

animal_crackers('Levelheaded Llama')
```

```python
# BUG-05 — args_kwargs.ipynb
def theMatrix(x, *args, **kwargs):
    kwargs['name'] = 'Trinity'
    newArgs = args + ('extra', )    # assigned as newArgs
    bar(x, *new_args, **kwargs)     # referenced as new_args — NameError

# FIX
def the_matrix(x, *args, **kwargs):
    kwargs['name'] = 'Trinity'
    new_args = args + ('extra', )
    bar(x, *new_args, **kwargs)
```

**Modernisation Actions**:
- Rename all `camelCase` function names to `snake_case`: `shuffleList` → `shuffle_list`, `playerGuess` → `player_guess`, `checkGuess` → `check_guess`.
- `args_kwargs.ipynb` contains a `super().__init__()` example in a wrapper class. `super()` is a method resolution order concept and belongs in `05-oop/`. Remove from this notebook and add a forward reference: *"The `super()` call visible in the wrapper example is covered with full context in `05-oop/oop_advanced.ipynb`."*

---

### `04-error-handling/`

**Objective**: Write robust code that fails gracefully using Python's exception model.

| File | Legacy Source | Concepts |
|---|---|---|
| `errors_and_exceptions.ipynb` | `[Notes] Errors & Exception Handling.ipynb` | `try`/`except`/`else`/`finally`, `IOError`, `ZeroDivisionError`, file write guard |
| `exercises.ipynb` | `[Short Exercise] Errors and Exceptions.ipynb` | Applying exception handling, `ZeroDivisionError` with `finally` |

**Bug Fixes Required**:

```python
# BUG-01 — exercises.ipynb
# Source: [Short Exercise] Errors and Exceptions.ipynb (canonical)
# Note: Error-checkpoint.ipynb in .ipynb_checkpoints/ is an auto-save artifact, NOT the source.

i = input('Passwords Required (1-10): ')  # i is str
while i > 0:                               # TypeError: '>' not supported between str and int
    ...

# FIX
i = int(input('Passwords Required (1-10): '))
while i > 0:
    ...
```

**Modernisation Actions**:
- Replace bare `except:` clauses with explicit exception types. Bare `except:` catches `KeyboardInterrupt` and `SystemExit`, masking interpreter signals.
  ```python
  # BEFORE
  except:
  # AFTER
  except (IOError, OSError):
  ```
- Introduce `with` statement for file handling as a direct continuation from `01-basics/input_output.ipynb`.

---

### `05-oop/`

**Objective**: Apply object-oriented principles: encapsulation, inheritance, polymorphism, and Python's dunder protocol.

| File | Legacy Source | Concepts |
|---|---|---|
| `oop_fundamentals.ipynb` | `[Notes] - OOP (Object Orientated Programming).ipynb` | `class`, `__init__`, `self`, attributes, methods |
| `oop_advanced.ipynb` | `[Notes] - OOP (Object Orientated Programming) Extended Version.ipynb` | Inheritance, `super().__init__()`, polymorphism, class composition |
| `special_methods.ipynb` | `[Notes] - OOP (Object Orientated Programming) - Special Methods Revisited.ipynb` | `__str__`, `__len__`, `__del__`, operator overloading |

The legacy repo contains three distinct OOP notebooks mapping 1:1 to the three files above. No content split is required — rename only.

**Bug Fixes Required**:

```python
# BUG-02 — oop_advanced.ipynb
Arsenal = Team('Arsenal')
Stadium = Music('Stadium')   # NameError: 'Music' is not defined
                             # Class is defined as Sound, not Music

# FIX
Arsenal = Team('Arsenal')
Stadium = Sound('Stadium')
```

**Modernisation Actions**:
- Replace old-style explicit super calls with Python 3 idiomatic form throughout `oop_advanced.ipynb`:
  ```python
  # BEFORE
  Premiership.__init__(self)

  # AFTER
  super().__init__()
  ```
- The `Glute` class (anatomy example in `oop_advanced.ipynb`) uses `print()` inside every method with no `return` value. Refactor to return values and call `print()` at the call site — this teaches the distinction between computation and presentation, which is a core OOP design principle.

---

### `06-modules-and-packages/`

**Objective**: Understand Python's import system, write reusable modules, and use pip to install third-party packages.

| File | Legacy Source | Concepts |
|---|---|---|
| `modules_and_packages.ipynb` | `[Notes] Modules and Packages.ipynb` | `import`, `from ... import`, `dir()`, `help()`, `__init__.py`, package structure |
| `pip_and_pypi.ipynb` | `[Notes] Modules & Packages - Pip Install and PyPi.ipynb` | pip, PyPI, `.whl`, installing packages |

**Supporting Files** (`examples/`):

| File | Legacy Source | Purpose |
|---|---|---|
| `examples/__init__.py` | **New file** | Makes `examples/` a proper importable package |
| `examples/drinks.py` | `drinks.py` | Minimal module — `def pineapple_grapefruit(): ...` |
| `examples/demo.py` | `demo.py` | Demonstrates `import examples.drinks; examples.drinks.pineapple_grapefruit()` |

**Modernisation Actions**:
- Rename `drinks.py::pineappleGrapefruit` → `pineapple_grapefruit` (PEP 8 function naming).
- Update `demo.py` import path after move to `examples/`: `import examples.drinks`.
- Add a note to `pip_and_pypi.ipynb` on the build toolchain change:
  ```
  Legacy (pre-PEP 517):  python setup.py bdist_wheel
  Modern  (PEP 517):     python -m build   # pip install build
  ```

---

### `07-projects/`

**Objective**: Apply all prior modules to build self-contained, interactive programs.

#### `project-01-confidence-game/`

A CLI implementation of Three-Card Monte using functions, user input validation, and the `random` module.

| File | Legacy Source | Concepts Applied |
|---|---|---|
| `warmup_exercises.ipynb` | `[Milestone project 1] Warmup Project Exercises.ipynb` | Input validation, `while` loops, `str.isdigit()`, `IPython.display.clear_output` |
| `confidence_game.ipynb` | `[Sandpit] A Confidence Game...ipynb` + `[4. Interaction] Simple User Interaction.ipynb` ² | Functional decomposition, `shuffle`, game loop |

² `[4. Interaction] Simple User Interaction.ipynb` contains board display scaffolding relevant to the game loop. Merge its `displayBoard` content into `confidence_game.ipynb` during Phase 2 and discard the source file — its content is not substantial enough to warrant a standalone notebook.

**Bug Fixes Required**:

```python
# BUG-03 — confidence_game.ipynb
def playerGuess():
    guess = ''
    while guess not in ['0', '1', '2']:
        guess = input("Pick a number: 0, 1 or 2")
        return int(guess)   # BUG: return INSIDE the while loop
                            # exits on first iteration regardless of input; validation never runs

# FIX
def player_guess():
    guess = ''
    while guess not in ['0', '1', '2']:
        guess = input("Pick a number: 0, 1 or 2  ")
    return int(guess)       # return OUTSIDE the while loop
```

```python
# BUG-06 — warmup_exercises.ipynb
from IPython.display import clear_output   # imported correctly as clear_output

clearOutput()   # NameError: clear_output is the correct name; clearOutput does not exist

# FIX
clear_output()
```

#### `project-02-card-game/`

A full implementation of Bicycle (War) using interacting OOP classes: `Card`, `Deck`, `Player`.

| File | Legacy Source | Concepts Applied |
|---|---|---|
| `bicycle_war.ipynb` | `[Milestone Project 2] - A Card Game - Bicycle aka War.ipynb` | Classes, composition, `__str__`, `__repr__`, list operations, game loop |

**Known Issue**: The legacy notebook contains a traceback originating from `ipykernel/iostream.py` — a Jupyter I/O race condition on `print()` inside a tight game loop. This is environmental (Anaconda 3.8 kernel scheduling), not a logic error. Resolved by upgrading to Python 3.11 + JupyterLab 4.

---

### `scratch/`

Non-reviewed, experimental notebooks. Not part of the learning sequence or CI validation. A `.gitkeep` file maintains the directory.

**Governance**: Scratch content is reviewed at the start of each curriculum expansion cycle. Promotion criteria: content maps to an identified curriculum gap and passes `pytest --nbmake` without modification. Deletion criteria: content has been in scratch for more than one revision cycle with no promotion action recorded.

---

## Deferred Content Register

Content from the legacy repository not included in the current curriculum scope. Dispositions are recorded here as architectural decisions, not silently discarded.

| Item | Legacy Source | Disposition | Rationale | Backlog ID |
|---|---|---|---|---|
| HTTP request exploration | `.ipynb_checkpoints/[How] Url Get request-checkpoint.ipynb` | Moved to `scratch/url_get_request.ipynb` | Incomplete; uses `requests` + `BeautifulSoup`. No corresponding curriculum module exists. Candidate for a future `08-web-and-apis/` module. | `DEF-01` |

---

## Current State vs. Proposed State

### Naming Convention

| Current (Legacy) | Proposed | Reason |
|---|---|---|
| `[Notes] Basic Input Output.ipynb` | `input_output.ipynb` | No brackets; no prefix; `snake_case`; shell-safe |
| `[Section 6] Function Practice Exercises - Level Two {Code Along}.ipynb` | `exercises_level_two.ipynb` | Section numbers belong in `README.md`, not filenames |
| `[Sandpit] A Confidence Game...` | `confidence_game.ipynb` in `project-01-confidence-game/` | Sandpit content is curriculum content with a bug; directory provides context |
| `[Milestone Project 2] - A Card Game...` | `bicycle_war.ipynb` | Project context is established by the parent directory, not the filename |
| `[Self Help] Python/Notes - Learning Resources/` | `python-learning-fundamentals/` (repo root) | Brackets, spaces, and nested navigation layers removed |

### Directory Structure

| Current (Legacy) | Issue | Proposed |
|---|---|---|
| `.ipynb_checkpoints/` tracked in git | Auto-save artifacts — never source of truth | Added to `.gitignore`; purged from history |
| `Notes - Learning Resources/` | Spaces require quoting in all CLI contexts | Eliminated — content elevated to numbered top-level modules |
| Single `[Self Help] Python/` top-level folder | Navigation layer with no value in a single-language repo | Eliminated |
| `Modules & Packages/drinks.py` alongside notebooks | `.py` files mixed with `.ipynb` notes in same directory | Moved to `06-modules-and-packages/examples/` |
| No `__init__.py` in `examples/` | `examples/` cannot be imported as a package | Add `examples/__init__.py` |

### Python Version

| Technology / Approach | Initial Cost | Maintenance Overhead | Security Risk | Scale Limit | Verdict |
|---|---|---|---|---|---|
| **Python 3.8.3** (Anaconda, EOL Oct 2024) | None — already running | None | **Critical** — no security patches post-EOL; any 3.8.x CVE is permanently unpatched | N/A | **Retire immediately** |
| **Python 3.11** (LTS, EOL Oct 2027) | Medium — re-run all notebooks; update kernel metadata | Low | Low | N/A | **Adopt** |

---

## Migration Phases

### Phase 1 — Environment Preparation

| Phase | Objective | Technical Prerequisites | Measurable Success Criteria |
|---|---|---|---|
| 1 | Create new GitHub org repo `python-learning-fundamentals` | GitHub account with org admin rights | Repo exists at target URL with no commits; default branch is `main` |
| 1 | Create `environment.yml` targeting Python 3.11, JupyterLab 4.* | Miniconda ≥ 23.x installed | `conda env create -f environment.yml && conda activate py-fundamentals` completes without error |
| 1 | Add `.gitignore` (Jupyter, Conda, OS, IDE excludes) | — | `git status` shows no `.ipynb_checkpoints/` entries after adding notebooks |
| 1 | Install `nbstripout` as a git pre-commit hook | `conda activate py-fundamentals` | `cat .git/config \| grep filter` shows `nbstripout` filter registered; committing a notebook with output cells produces a clean diff |

```bash
# Phase 1 — nbstripout installation
pip install nbstripout
nbstripout --install   # registers git filter in .git/config
nbstripout --status    # verify: "nbstripout is installed in this repository"
```

### Phase 2 — Content Migration and Renaming

| Phase | Objective | Technical Prerequisites | Measurable Success Criteria |
|---|---|---|---|
| 2 | Delete all `.ipynb_checkpoints/` directories from legacy clone | Phase 1 complete | `find . -type d -name ".ipynb_checkpoints"` returns nothing |
| 2 | Clear all notebook output cells to remove embedded legacy paths | `nbconvert` installed | `grep -r "amcm_dev01" --include="*.ipynb" .` returns nothing |
| 2 | Rename all notebooks per new `snake_case` convention using mapping table below | — | `find . -name "*.ipynb" \| grep -E "[\[\]\{\} ]"` returns nothing |
| 2 | Move `drinks.py` / `demo.py` into `06-modules-and-packages/examples/`; add `__init__.py` | — | `python -c "import examples.drinks"` succeeds from module root |
| 2 | Create `02-control-flow/loops_and_conditionals.ipynb` as a complete notebook | — | Notebook covers `for`, `while`, `break`, `continue`, `enumerate()`, `zip()`; runs without error |
| 2 | Merge `[4. Interaction] Simple User Interaction.ipynb` content into `confidence_game.ipynb` | — | `displayBoard` function present in `confidence_game.ipynb`; source file deleted |

```bash
# Phase 2 — clear outputs from legacy clone before copy
find . -name "*.ipynb" -not -path "*/.ipynb_checkpoints/*" \
  -exec jupyter nbconvert --clear-output --inplace {} \;

# Phase 2 — delete checkpoints
find . -type d -name ".ipynb_checkpoints" -exec rm -rf {} +
```

### Phase 3 — Bug Fixes and Kernel Update

| Phase | Objective | Technical Prerequisites | Measurable Success Criteria |
|---|---|---|---|
| 3 | Apply BUG-01: `int(input(...))` in `04-error-handling/exercises.ipynb` | Phase 2 complete | Cell executes without `TypeError`; loop iterates correctly |
| 3 | Apply BUG-02: `Sound` not `Music` in `05-oop/oop_advanced.ipynb` | Phase 2 complete | Polymorphism cell executes; both `audio()` calls print without error |
| 3 | Apply BUG-03: `return` dedented outside `while` in `confidence_game.ipynb` | Phase 2 complete | `player_guess()` rejects invalid input; accepts `'0'`, `'1'`, `'2'` only |
| 3 | Apply BUG-04: Unified `animal_crackers` naming in `exercises_level_one.ipynb` | Phase 2 complete | All three test calls return without `NameError` |
| 3 | Apply BUG-05: `new_args` consistency in `args_kwargs.ipynb` | Phase 2 complete | Wrapper function cell executes without `NameError` |
| 3 | Apply BUG-06: `clear_output()` not `clearOutput()` in `warmup_exercises.ipynb` | Phase 2 complete | Input validation cell clears output and re-prompts correctly |
| 3 | Update kernel metadata in all notebooks to `py-fundamentals` (Python 3.11) | `py-fundamentals` Conda env active; `ipykernel` installed | `jupyter kernelspec list` shows `py-fundamentals`; no notebook references `python3 (3.8.3)` in kernel spec |

```bash
# Phase 3 — register the new kernel
conda activate py-fundamentals
python -m ipykernel install --user --name py-fundamentals --display-name "Python 3.11 (py-fundamentals)"

# Phase 3 — verify no notebooks reference the old kernel version
grep -r "3.8" --include="*.ipynb" . | grep "version"
# Expected: zero matches

# Phase 3 — verify snake_case compliance across .py files
grep -rn --include="*.py" '[a-z][A-Z]' 06-modules-and-packages/examples/
# Expected: zero matches after renaming pineappleGrapefruit
```

### Phase 4 — Documentation and Validation

| Phase | Objective | Technical Prerequisites | Measurable Success Criteria |
|---|---|---|---|
| 4 | Add `README.md` to every module directory | Phase 3 complete | `find . -type d -not -path "*/.git/*" -not -path "*/scratch*" \| xargs -I{} test -f {}/README.md` — all pass |
| 4 | Install `pytest --nbmake` and validate all curriculum notebooks | `py-fundamentals` env active | `pytest --nbmake 01-basics/ 02-control-flow/ 03-functions/ 04-error-handling/ 05-oop/ 06-modules-and-packages/ 07-projects/` — zero failures |
| 4 | Create a `git clone --mirror` backup of legacy repo before archiving | — | Mirror clone exists and `git log --oneline` shows full legacy history |
| 4 | Archive legacy repo on GitHub; update description with redirect | All Phase 4 validation checks pass | Legacy repo is read-only; description reads "DEPRECATED — migrated to [URL]" |

---

## Risk Assessment

### Single Points of Failure

**SPOF-1 — `dictionaries.ipynb` sourced exclusively from a checkpoint file**
No canonical `.ipynb` counterpart for `Dictionaries in Python` exists outside `.ipynb_checkpoints/`. Checkpoints are not committed atomically and may diverge from the last known session state. If the checkpoint is corrupted or its output cells are incorrect, there is no recovery path. Mitigation: manually verify all cell outputs before Phase 2 commit and add a note in the notebook's first markdown cell that the source was a checkpoint.

**SPOF-2 — No pre-migration backup of legacy repo**
GitHub allows owners to delete archived repositories. If the legacy repo is deleted post-archival, all commit history is permanently lost. The Phase 4 `git clone --mirror` step is mandatory, not optional.

**SPOF-3 — `environment.yml` pins `jupyterlab` without a version constraint**
The generated `environment.yml` artifact pins `jupyterlab` without a version specifier. The architecture mandates `jupyterlab=4.*`. A future Conda solve could install JupyterLab 5 (when released) and introduce breaking changes silently. Tracked as `ENV-01` in `BACKLOG.md`.

**SPOF-4 — Embedded legacy path in `pwd` cell outputs**
`[Notes] Basic Input Output.ipynb` contains a rendered `pwd` output cell showing `/Users/amcm_dev01/github/MacOS_Repository_Restructured/...`. This is cleared by `jupyter nbconvert --clear-output` in Phase 2 — but if `nbstripout` is not installed before the first commit (Phase 1), any re-execution and commit of this notebook will re-introduce the path. The `nbstripout` installation in Phase 1 is the control that prevents this.

### What Breaks at 10x Scale

At 10x the current notebook count (~18 → ~180):

- **Kernel drift becomes undetectable** without CI. A `Restart & Run All` discipline that is manageable across 18 notebooks is not sustainable across 180. The `pytest --nbmake` CI workflow added in Phase 4 is the correct control. Without it, notebook output drift accumulates silently and the curriculum cannot be trusted.
- **The `scratch/` directory governance policy breaks down**. At 10x experimental notebooks with no automated promotion/deletion enforcement, scratch becomes structurally identical to the legacy repo's problem. The lifecycle review cadence defined in the `scratch/` section above must be treated as a process constraint, not a guideline.
- **Sequential module numbering requires renumbering on insert**. Adding a new module between `02` and `03` requires renaming all subsequent directories. At 10x modules this is a high-friction operation. Mitigation at that scale: introduce a `MODULES.md` manifest file that defines the canonical learning sequence independently of directory names, allowing sparse numbering (`01`, `02`, `04` with a gap) without breaking the sequence.

### Vendor Lock-in Vulnerabilities

- **Conda**: `environment.yml` is Conda-specific. If the organisation moves to a pure pip workflow, generate a pip fallback: `conda run -n py-fundamentals pip freeze > requirements.txt`. Run this after every `environment.yml` change.
- **JupyterLab**: All content is `.ipynb` format. IPython magic commands (`%reset`, `%pwd`, `from IPython.display import clear_output`) are JupyterLab-specific. If migrating to VS Code Jupyter extension or Google Colab, audit all magic usage: `grep -rn "IPython\|get_ipython\|%reset\|%pwd" --include="*.ipynb" .` before migration.
- **GitHub**: The deprecation strategy (archived repo + description redirect) is GitHub-specific. The `DEPRECATED.md` file in the legacy repo root is the platform-agnostic signal and must be committed before archiving in case the organisation migrates to GitLab or Bitbucket.
- **`nbstripout`**: The output-stripping strategy is tied to this library. It is mature (4,000+ GitHub stars, active maintenance) but if it becomes unmaintained, the git hook silently stops running with no warning. Verify on a schedule: `nbstripout --status` in CI.