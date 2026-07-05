# MIGRATION.md

Operational runbook for migrating from `amcm_dev01/MacOS_Repository_Restructured` to the new SAEHL organisation repository `python-learning-fundamentals`.

---

## Pre-Migration Checklist

- [ ] New GitHub account/org created
- [ ] SSH key or PAT configured for new account
- [ ] Miniconda ≥ 23.x installed on working machine
- [ ] Legacy repo cloned locally and working directory clean (`git status` shows nothing staged)

---

## Step 1 — Clean Outputs and Remove Checkpoints from Legacy Clone

Run from the root of your local clone of the legacy repo.

```bash
# Install nbconvert if not present
pip install nbconvert

# Clear all output cells to remove embedded legacy paths from stdout
find . -name "*.ipynb" -not -path "*/.ipynb_checkpoints/*" \
  -exec jupyter nbconvert --clear-output --inplace {} \;

# Confirm .ipynb_checkpoints directories exist (we're about to delete them)
find . -type d -name ".ipynb_checkpoints"

# Delete all checkpoint directories
find . -type d -name ".ipynb_checkpoints" -exec rm -rf {} +

# Verify
find . -type d -name ".ipynb_checkpoints"   # Should return nothing
```

---

## Step 2 — Create New Repository Structure

```bash
# Create the new repo root directory (separate from the legacy clone)
mkdir python-learning-fundamentals
cd python-learning-fundamentals
git init
git branch -M main
```

Create the directory skeleton:

```bash
mkdir -p \
  01-basics \
  02-control-flow \
  03-functions \
  04-error-handling \
  05-oop \
  06-modules-and-packages/examples \
  07-projects/project-01-confidence-game \
  07-projects/project-02-card-game \
  scratch

touch scratch/.gitkeep
```

---

## Step 3 — Add Root Configuration Files

Add `.gitignore` at repo root:

```bash
cat > .gitignore << 'EOF'
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
env/

# OS
.DS_Store
Thumbs.db
*.swp
EOF
```

Add `environment.yml`:

```bash
cat > environment.yml << 'EOF'
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
EOF
```

---

## Step 4 — Copy and Rename Notebooks

Use the mapping table below. Paths are relative to the legacy repo root (`[Self Help] Python/Notes - Learning Resources/`).

| Source (Legacy) | Destination (New) |
|---|---|
| `The Basics/[Notes] Basic Input Output.ipynb` | `01-basics/input_output.ipynb` |
| `The Basics/[Notes] Sets.ipynb` | `01-basics/sets.ipynb` |
| `.ipynb_checkpoints/Dictionaries in Python-checkpoint.ipynb` | `01-basics/dictionaries.ipynb` (checkpoint only — verify content matches canonical if one exists) |
| `Python Statements/[Notes] List Comprehensions.ipynb` | `02-control-flow/list_comprehensions.ipynb` |
| `Functions & Methods/[Notes] Methods and The Python Documentation.ipynb` | `03-functions/methods_and_documentation.ipynb` |
| `Functions & Methods/[Notes] Interactions Between Python Functions.ipynb` | `03-functions/function_interactions.ipynb` |
| `Functions & Methods/[Notes] More *Args & **Kwargs.ipynb` | `03-functions/args_kwargs.ipynb` |
| `Functions & Methods/[Section 6] Function Practice Exercises {Code Along}.ipynb` | `03-functions/exercises_level_one.ipynb` |
| `Functions & Methods/[Section 6] Function Practice Exercises - Level Two {Code Along}.ipynb` | `03-functions/exercises_level_two.ipynb` |
| `Errors & Exceptions/[Notes] Errors & Exception Handling.ipynb` | `04-error-handling/errors_and_exceptions.ipynb` |
| `Errors & Exceptions/[Short Exercise] Errors and Exceptions.ipynb` | `04-error-handling/exercises.ipynb` |
| `OOP/[Notes] - OOP (Object Orientated Programming).ipynb` | `05-oop/oop_fundamentals.ipynb` |
| `OOP/[Notes] - OOP (Object Orientated Programming) Extended Version.ipynb` | `05-oop/oop_advanced.ipynb` |
| `OOP/[Notes] - OOP (Object Orientated Programming) - Special Methods Revisited.ipynb` | `05-oop/special_methods.ipynb` |
| `Modules & Packages/[Notes] Modules and Packages.ipynb` | `06-modules-and-packages/modules_and_packages.ipynb` |
| `Modules & Packages/[Notes] Modules & Packages - Pip Install and PyPi.ipynb` | `06-modules-and-packages/pip_and_pypi.ipynb` |
| `Modules & Packages/drinks.py` | `06-modules-and-packages/examples/drinks.py` |
| `Modules & Packages/demo.py` | `06-modules-and-packages/examples/demo.py` |
| `Milestone Project 1/[Milestone project 1] Warmup Project Exercises.ipynb` | `07-projects/project-01-confidence-game/warmup_exercises.ipynb` |
| `Milestone Project 1/[4. Interaction] Simple User Interaction.ipynb` | Merge content into `warmup_exercises.ipynb` or discard if redundant |
| `Milestone Project 1/[Sandpit] A Confidence Game...ipynb` | `07-projects/project-01-confidence-game/confidence_game.ipynb` |
| `Milestone Project 2/[Milestone Project 2] - A Card Game - Bicycle aka War.ipynb` | `07-projects/project-02-card-game/bicycle_war.ipynb` |

Notebooks tracked only in `.ipynb_checkpoints/` with no canonical equivalent:
- `.ipynb_checkpoints/[How] Url Get request-checkpoint.ipynb` → `scratch/url_get_request.ipynb` (incomplete, not in curriculum)
- `.ipynb_checkpoints/Error-checkpoint.ipynb` → content merged into `04-error-handling/exercises.ipynb` after bug fix

---

## Step 5 — Apply Bug Fixes

Open each affected notebook in JupyterLab and make the corrections below before the first commit.

### Fix 1 — `04-error-handling/exercises.ipynb`

```python
# BEFORE
i = input('Passwords Required (1-10): ')
while i > 0:

# AFTER
i = int(input('Passwords Required (1-10): '))
while i > 0:
```

### Fix 2 — `05-oop/oop_advanced.ipynb`

```python
# BEFORE
Stadium = Music('Stadium')

# AFTER
Stadium = Sound('Stadium')
```

### Fix 3 — `07-projects/project-01-confidence-game/confidence_game.ipynb`

```python
# BEFORE
def playerGuess():
    guess = ''
    while guess not in ['0','1','2']:
        guess = input("Pick a number: 0, 1 or 2")
        return int(guess)      # ← indented inside while

# AFTER
def player_guess():
    guess = ''
    while guess not in ['0', '1', '2']:
        guess = input("Pick a number: 0, 1 or 2  ")
    return int(guess)          # ← dedented, outside while
```

### Fix 4 — `03-functions/exercises_level_one.ipynb`

```python
# BEFORE (inconsistent naming across cells)
def animaCrackers(text): ...
animalCrackers('Levelheaded Llama')

# AFTER (consistent snake_case)
def animal_crackers(text):
    word_list = text.lower().split()
    return word_list[0][0] == word_list[1][0]

animal_crackers('Levelheaded Llama')
```

### Fix 5 — `03-functions/args_kwargs.ipynb`

```python
# BEFORE
def theMatrix(x, *args, **kwargs):
    kwargs['name'] = 'Trinity'
    newArgs = args + ('extra', )
    bar(x, *new_args, **kwargs)   # NameError: new_args undefined

# AFTER
def the_matrix(x, *args, **kwargs):
    kwargs['name'] = 'Trinity'
    new_args = args + ('extra', )
    bar(x, *new_args, **kwargs)
```

---

## Step 6 — Validate All Notebooks

```bash
# Install nbmake for notebook testing
pip install pytest nbmake

# Run all notebooks top-to-bottom
pytest --nbmake 01-basics/ 02-control-flow/ 03-functions/ \
               04-error-handling/ 05-oop/ 06-modules-and-packages/ \
               07-projects/

# Zero failures = migration complete
```

Expected: any notebooks using `input()` interactively will fail in non-interactive `nbmake` runs. Refactor those cells to use hardcoded values with a comment: `# In interactive use, replace with: input("...")`.

---

## Step 7 — Initial Commit to New Repo

```bash
cd python-learning-fundamentals

# Add README and docs first
git add README.md ARCHITECTURE.md MIGRATION.md .gitignore environment.yml
git commit -m "chore: initial repo structure and documentation"

# Add modules
git add 01-basics/ 02-control-flow/ 03-functions/ 04-error-handling/
git commit -m "feat: add fundamentals modules 01-04 (basics through error handling)"

git add 05-oop/ 06-modules-and-packages/
git commit -m "feat: add OOP and modules curriculum (05-06)"

git add 07-projects/
git commit -m "feat: add milestone projects 01 and 02"

git add scratch/
git commit -m "chore: add scratch directory for experimental notebooks"

# Push to new remote
git remote add origin git@github.com:<new-org>/python-learning-fundamentals.git
git push -u origin main
```

---

## Step 8 — Deprecate Legacy Repository

On GitHub (`amcm_dev01/MacOS_Repository_Restructured`):

1. Go to **Settings → General**
2. Update **Description** to: `DEPRECATED — Migrated to [new repo URL]. See DEPRECATED.md for details.`
3. Add a `DEPRECATED.md` at the legacy repo root:

```markdown
# DEPRECATED

This repository has been migrated and restructured.

**New location**: https://github.com/<new-org>/python-learning-fundamentals

This repository is archived and will not receive further updates.
All content, with bug fixes and restructuring applied, is available at the link above.
```

4. Go to **Settings → Danger Zone → Archive this repository**
5. Confirm archiving — the repo becomes read-only

---

## Post-Migration Verification

- [ ] New repo visible at expected URL
- [ ] `conda env create -f environment.yml` succeeds on a clean machine
- [ ] All notebooks run without errors under Python 3.11
- [ ] `.ipynb_checkpoints/` does not appear in `git status` after editing a notebook
- [ ] Legacy repo is archived and description points to new repo
- [ ] No hardcoded paths referencing `/Users/amcm_dev01/` remain in any cell output