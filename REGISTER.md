# REGISTER.md — python-learning-fundamentals
<!-- Live register: update checkboxes in-place as work is completed -->

---

## How to Use

This is a flat, checkbox-driven register. Each item has a stable ID that can be referenced in commit messages and PR descriptions (e.g. `fix: BUG-03 player_guess return indentation`).

- Check an item with `[x]` when complete.
- Add a completion note and date in the **Notes** column where useful.
- Do not delete completed items — the register is a historical record.
- New items are appended to the bottom of their section with the next sequential ID.

---

## Priority Legend

| Priority | Meaning |
|---|---|
| P0 | Blocking — migration cannot be considered complete without this |
| P1 | High — should be resolved in the current migration cycle |
| P2 | Medium — improves quality; does not block the migration |
| P3 | Low — deferred enhancement; scheduled for a future revision cycle |

---

## BUG — Confirmed Runtime Bugs

All six bugs are P0. They exist in active learning notebooks and will cause failures during `pytest --nbmake` validation (Phase 4).

| ID | Done | Priority | Description | Target File | Acceptance Criteria |
|---|---|---|---|---|---|
| BUG-01 | [ ] | P0 | `TypeError`: `i = input(...)` returns `str`; `while i > 0` compares `str` to `int` | `04-error-handling/exercises.ipynb` | Fix to `int(input(...))`. Cell executes without `TypeError`. |
| BUG-02 | [ ] | P0 | `NameError`: `Stadium = Music('Stadium')` — class is defined as `Sound`, not `Music` | `05-oop/oop_advanced.ipynb` | Fix to `Sound('Stadium')`. Both `audio()` calls print without error. |
| BUG-03 | [ ] | P0 | Logic error: `return int(guess)` is inside the `while` loop — input validation never executes | `07-projects/project-01-confidence-game/confidence_game.ipynb` | Dedent `return` to outside the `while` block. Function rejects invalid input before returning. |
| BUG-04 | [ ] | P0 | `NameError`: `animaCrackers` defined; `animalCrackers` called — two different spellings across cells | `03-functions/exercises_level_one.ipynb` | Unified to `animal_crackers` (snake_case). All three test call cells return without error. |
| BUG-05 | [ ] | P0 | `NameError`: `newArgs` assigned; `new_args` referenced in same function — undefined variable | `03-functions/args_kwargs.ipynb` | Unified to `new_args`. Wrapper function cell executes without `NameError`. |
| BUG-06 | [ ] | P0 | `NameError`: `clearOutput()` called; correct name is `clear_output` (imported from `IPython.display`) | `07-projects/project-01-confidence-game/warmup_exercises.ipynb` | Fix to `clear_output()`. Input validation cell clears output and re-prompts on invalid entry. |

---

## STR — Structural Improvements

| ID | Done | Priority | Description | Target File / Location | Acceptance Criteria |
|---|---|---|---|---|---|
| STR-01 | [ ] | P0 | Create `git clone --mirror` backup of legacy repo before archiving — deletion of archived repos is permitted on GitHub | Local filesystem | Mirror clone exists; `git log --oneline` from mirror shows full legacy history |
| STR-02 | [ ] | P1 | Add `__init__.py` to `examples/` so it functions as an importable package | `06-modules-and-packages/examples/__init__.py` | `python -c "import examples.drinks"` succeeds from the module directory |
| STR-03 | [ ] | P1 | Update `demo.py` import path after move from `Modules & Packages/` to `examples/` | `06-modules-and-packages/examples/demo.py` | `python examples/demo.py` executes without `ModuleNotFoundError` |
| STR-04 | [ ] | P1 | Pin `jupyterlab=4.*` in `environment.yml` — currently unpinned; a future Conda solve could silently install JupyterLab 5 | `environment.yml` | `conda env create -f environment.yml` installs JupyterLab 4.x; `jupyter lab --version` confirms |
| STR-05 | [ ] | P2 | Add `README.md` to every module directory | All numbered module dirs + `07-projects/` subdirs | `find . -maxdepth 2 -type d \| xargs -I{} test -f {}/README.md` — all pass |
| STR-06 | [ ] | P2 | Create `requirements.txt` as a pip fallback from the Conda environment | Repo root | `pip install -r requirements.txt` installs an equivalent working environment on a non-Conda system |

---

## MOD — Modernisation & PEP 8

| ID | Done | Priority | Description | Target File | Acceptance Criteria |
|---|---|---|---|---|---|
| MOD-01 | [ ] | P1 | Replace bare `open()` calls with `with open(...) as f:` context managers — legacy code leaks file handles | `01-basics/input_output.ipynb` | No bare `open()` call without a matching `.close()` or context manager remains in notebook |
| MOD-02 | [ ] | P1 | Replace bare `except:` clauses with explicit exception types — bare `except` catches `KeyboardInterrupt` and `SystemExit` | `04-error-handling/errors_and_exceptions.ipynb`, `exercises.ipynb` | `grep -n "except:"` returns zero unqualified matches across both notebooks |
| MOD-03 | [ ] | P1 | Replace `ParentClass.__init__(self)` with `super().__init__()` throughout inheritance examples | `05-oop/oop_advanced.ipynb` | `grep -n "__init__(self)" oop_advanced.ipynb` returns zero old-style super calls |
| MOD-04 | [ ] | P1 | Rename `pineappleGrapefruit` → `pineapple_grapefruit` in `drinks.py` and update call site in `demo.py` | `06-modules-and-packages/examples/drinks.py`, `demo.py` | `python examples/demo.py` runs; no `camelCase` identifiers in either file |
| MOD-05 | [ ] | P1 | Rename all `camelCase` function names to `snake_case` across all notebooks: `shuffleList`, `playerGuess`, `checkGuess`, `displayBoard`, `positionIndex`, `positionDecision` | `03-functions/`, `07-projects/project-01-confidence-game/` | `grep -rn --include="*.ipynb" '[a-z][A-Z]' .` returns zero matches outside comments |
| MOD-06 | [ ] | P1 | Remove `super().__init__()` from `args_kwargs.ipynb` concept scope — it is an OOP concept and creates a forward dependency before classes are introduced; add a forward reference note | `03-functions/args_kwargs.ipynb` | Notebook runs without requiring understanding of class inheritance |
| MOD-07 | [ ] | P2 | Add build toolchain note to `pip_and_pypi.ipynb`: `setup.py bdist_wheel` (pre-PEP 517) → `python -m build` (PEP 517) | `06-modules-and-packages/pip_and_pypi.ipynb` | Note cell added with before/after code block |
| MOD-08 | [ ] | P2 | Refactor `Glute` class methods in `oop_advanced.ipynb` to `return` values instead of calling `print()` inside the method body; update call sites | `05-oop/oop_advanced.ipynb` | All class methods return strings; `print(Glute.composition())` pattern used at call sites |
| MOD-09 | [ ] | P2 | Verify `dictionaries.ipynb` cell `my_dictionary1['key2']` returns the list `[0, 1, 2, 3, 4]` not the string `'[0,1,2,3,4]'` — corrupted output cell observed in checkpoint source | `01-basics/dictionaries.ipynb` | Cell output shows `[0, 1, 2, 3, 4]` with correct type `<class 'list'>` |
| MOD-10 | [ ] | P3 | Add type hints to all standalone `.py` files (`drinks.py`, `demo.py`) as a demonstration of modern Python practice | `06-modules-and-packages/examples/` | Both files pass `mypy examples/` with zero errors |

---

## GAP — Content Gaps

| ID | Done | Priority | Description | Target File | Acceptance Criteria |
|---|---|---|---|---|---|
| GAP-01 | [ ] | P1 | Create `loops_and_conditionals.ipynb` — `02-control-flow/` currently starts with comprehensions with no prerequisite loop foundations | `02-control-flow/loops_and_conditionals.ipynb` | Notebook covers `for`, `while`, `break`, `continue`, `enumerate()`, `zip()`, ternary expressions; passes `pytest --nbmake` |
| GAP-02 | [ ] | P2 | `special_methods.ipynb` covers `__str__` and `__del__` but not `__len__`, `__add__`, or `__repr__` as documented in the module objective | `05-oop/special_methods.ipynb` | Notebook includes worked examples for `__len__`, `__add__`, and `__repr__`; objective table in module `README.md` matches content |
| GAP-03 | [ ] | P3 | `03-functions/` has no coverage of `lambda`, `map()`, `filter()`, or `sorted()` with a `key=` argument — these are commonly needed before comprehensions in `02-control-flow/` | `03-functions/` — new notebook `lambda_and_higher_order.ipynb` | Notebook covers `lambda`, `map()`, `filter()`, `sorted(key=)` with worked examples; passes `pytest --nbmake` |
| GAP-04 | [ ] | P3 | No coverage of generators or `yield` in the curriculum — relevant after `03-functions/` | `03-functions/` — new notebook `generators.ipynb` | Notebook covers `yield`, generator expressions, and `next()`; distinguishes from list comprehensions |

---

## DEF — Deferred Content

Items explicitly out of scope for this migration cycle. Recorded here so the disposition is auditable.

| ID | Done | Priority | Description | Legacy Source | Disposition | Next Action |
|---|---|---|---|---|---|---|
| DEF-01 | [ ] | P3 | HTTP request exploration: `requests.get()`, `BeautifulSoup`, status codes | `.ipynb_checkpoints/[How] Url Get request-checkpoint.ipynb` | Moved to `scratch/url_get_request.ipynb`. Incomplete; not curriculum-ready. | Evaluate for `08-web-and-apis/` module in next revision cycle |
| DEF-02 | [ ] | P3 | Design `08-web-and-apis/` module | N/A — new content | Not started | Depends on `DEF-01` evaluation; schedule after all P0/P1 items resolved |

---

## CI — CI/CD & Tooling

| ID | Done | Priority | Description | Target | Acceptance Criteria |
|---|---|---|---|---|---|
| CI-01 | [ ] | P0 | Install and configure `nbstripout` as a git pre-commit hook | Repo root (Phase 1) | `nbstripout --status` confirms hook is installed; committing a notebook with output produces a clean diff |
| CI-02 | [ ] | P1 | Add `pytest --nbmake` to validate all curriculum notebooks end-to-end | `environment.yml` + Phase 4 | `pytest --nbmake 01-basics/ ... 07-projects/` runs with zero failures from a clean kernel |
| CI-03 | [ ] | P2 | Create GitHub Actions workflow (`.github/workflows/validate-notebooks.yml`) to run `pytest --nbmake` on every push to `main` | `.github/workflows/` | Workflow triggers on push; fails the check if any notebook raises an unhandled exception |
| CI-04 | [ ] | P2 | Add `nbqa` + `flake8` for notebook code quality linting | `environment.yml` | `nbqa flake8 . --select=E,W,N8` produces a defined, reviewed set of accepted warnings; zero P0-level violations |
| CI-05 | [ ] | P3 | Create `.pre-commit-config.yaml` combining `nbstripout` + `nbqa` for a single pre-commit pipeline | Repo root | `pre-commit run --all-files` passes on all notebooks and `.py` files |
| CI-06 | [ ] | P3 | Add `nbstripout --status` check to CI workflow so a missing hook is caught automatically | `.github/workflows/` | Workflow fails with an explicit message if `nbstripout` filter is not installed in the cloned repo |

---

## GOV — Governance & Process

| ID | Done | Priority | Description | Target | Acceptance Criteria |
|---|---|---|---|---|---|
| GOV-01 | [ ] | P1 | Define and document branch strategy — main-only vs main + dev | `CONTRIBUTING.md` (new file) | Strategy documented; at minimum specifies whether direct push to `main` is permitted |
| GOV-02 | [ ] | P2 | Add `CONTRIBUTING.md` with: branch strategy, commit message format (e.g. `fix: BUG-ID description`), notebook validation steps before PR | Repo root | File exists; references `BACKLOG.md` ID format for commit messages |
| GOV-03 | [ ] | P2 | Define `scratch/` review cadence in `CONTRIBUTING.md` — promotion criteria, deletion criteria, review owner | `CONTRIBUTING.md` | Policy is written; mirrors the governance note in `ARCHITECTURE.md` |
| GOV-04 | [ ] | P3 | Add a `CHANGELOG.md` to track curriculum changes by version | Repo root | File exists; records v1.0.0 as the initial migration from `MacOS_Repository_Restructured` |

---

## ENV — Environment & Infrastructure

| ID | Done | Priority | Description | Target | Acceptance Criteria |
|---|---|---|---|---|---|
| ENV-01 | [ ] | P1 | Pin `jupyterlab=4.*` in `environment.yml` — currently unpinned in generated artifact | `environment.yml` | `conda search jupyterlab --channel conda-forge` confirms 4.x is installed; `jupyter lab --version` returns `4.*` |
| ENV-02 | [ ] | P1 | Register `py-fundamentals` ipykernel so all notebooks use the correct Python 3.11 interpreter | Phase 3 step | `jupyter kernelspec list` shows `py-fundamentals`; no notebook's kernel spec references `python3 (3.8.3)` |
| ENV-03 | [ ] | P2 | Generate `requirements.txt` pip fallback from the Conda environment | Repo root | `pip install -r requirements.txt` installs a working environment; `import jupyterlab` succeeds |
| ENV-04 | [ ] | P3 | Evaluate `pixi` as a next-generation Conda-compatible environment manager for a future migration | — | Decision recorded in `ARCHITECTURE.md` with a decision matrix entry; no action required now |

---

## Summary

| Section | Total Items | Open | Complete |
|---|---|---|---|
| BUG — Runtime Bugs | 6 | 6 | 0 |
| STR — Structural | 6 | 6 | 0 |
| MOD — Modernisation | 10 | 10 | 0 |
| GAP — Content Gaps | 4 | 4 | 0 |
| DEF — Deferred Content | 2 | 2 | 0 |
| CI — CI/CD & Tooling | 6 | 6 | 0 |
| GOV — Governance | 4 | 4 | 0 |
| ENV — Environment | 4 | 4 | 0 |
| **Total** | **42** | **42** | **0** |
