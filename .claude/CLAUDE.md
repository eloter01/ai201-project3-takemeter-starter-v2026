# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

act as an expert Python developer who writes clean, readable, and production-ready code. When writing Python code for me, you must strictly follow these software engineering principles:
1. Code Readability & Style
	* Follow PEP 8 guidelines strictly (naming conventions, spacing, line lengths).
	* Use descriptive, intention-revealing variable and function names.
	* Keep functions small, focused, and limited to a single responsibility.
2. Defensive Programming & Error Handling
	* Use specific exception handling (e.g., except ValueError, except FileNotFoundError) instead of a catch-all 'except Exception:'.
	* Use the "Look Before You Leap" (LBYL) or "Easier to Ask for Forgiveness than Permission" (EAFP) paradigms appropriately based on the context.
	* Implement explicit validation for function arguments (e.g., checking for None, empty bounds, or invalid types) before processing.
	* Use type hinting for function signatures to improve clarity.
3. Minimal & Meaningful Documentation
	* Do NOT write obvious comments that repeat what the code does (e.g., avoid # increment x by 1).
	* Only use comments to explain the "Why", not the "What" (e.g., complex algorithms, business logic rules, or non-obvious workarounds).
	* Include concise docstrings for functions detailing their purpose, parameters, and return values.
4. Modern Pythonic Practices
	* Use contextual managers (with statements) for resource management (files, network connections).
	* Leverage modern features like f-strings, list comprehensions (where readable), and the pathlib module where appropriate.
5. Incremental & Committable Deliveries
	* Do not implement the entire feature or fix all issues at once. Break the task down into small, independent, logical steps.
	* Present exactly ONE committable change at a time. A change is "committable" if it leaves the codebase in a working state with its own specific focus (e.g., adding a validation layer, implementing a single helper function, refactoring one block).
	* Provide the code for this step, along with a suggested Git commit message following Conventional Commits (e.g., "feat: add input validation for user email").
	* Pause and wait for my approval or feedback before providing the next step.
6. Scope Discipline
* Build only what the current spec section asks for. Later sections often change earlier assumptions, so code written ahead of the spec usually ends up being undone.
* Don't add features, parameters, config options, or abstractions the current section doesn't need, even if they look like obvious next steps. Sections 1 to 4 govern how you write what's in scope, not whether to expand it.
* The current spec is the one named under "Specs" below.
* If something in the current section is ambiguous, ask before implementing. For small gaps the spec doesn't cover, choose the simplest option and list it under "Assumptions" in your response.
* If something outside the current scope seems worth adding, suggest it in one line at the end instead of implementing it.
7. Prose writing
* any written prose must be written in simple language, equivalent to how the average college student would write. stay to the point, avoid overly verbose writing.

---

## Specs
- Feature specs live in docs/, one per phase
- Current phase: docs/SPEC_phase_2.md
- Earlier specs are historical; the code is the source of truth for what exists. Use it so your design choices don't conflict with later sections, but don't write code that only a later section needs.

## About this repo

TakeMeter is a CodePath AI201 student project, forked from a starter. The student picks a public online community, designs 2–4 labels for its posts, hand-labels 200+ posts, and fine-tunes `distilbert-base-uncased` to classify them. It runs over two units: unit 5 builds it (the spec is docs/SPEC.md) and unit 6, the current phase, tests it (the spec is docs/SPEC_phase_2.md). Everything runs locally. There are no API keys and no hosted models.

Most of the graded work is writing and data: `criteria.md`, `labels.csv`, and the README sections. The scripts and notebook are starter code. The student is expected to change only the settings at the top of notebook sections 1 and 2, plus `RUN_LABEL` in section 6.

## Commands

Always use the project venv. The `python` on PATH on this machine is 3.14, which the pinned packages don't support. The venv runs 3.12.

```bash
.venv/Scripts/python.exe test.py                              # environment check: Python version, venv, packages, disk, RAM, model cache
.venv/Scripts/python.exe baseline.py                          # unit 6: zero-shot baseline on test_split.csv, writes baseline_results.json
.venv/Scripts/python.exe baseline.py --trained results.json   # same, plus the Baseline vs. Trained table
.venv/Scripts/python.exe agreement.py                         # unit 6: my_staff_labels.csv vs. staff labels, writes agreement_results.json
```

- `test.py` checks the environment. It is not a test suite. There are no unit tests, no linter, and no build step.
- `baseline.py` downloads `facebook/bart-large-mnli` (~1.6 GB) the first time it runs.
- Milestone 1 also asks for a bare-label-names baseline run. There's no flag for it: point `--definitions` at a file that doesn't exist. Also set `--out`, or the run overwrites `baseline_results.json`. For example: `.venv/Scripts/python.exe baseline.py --definitions none --out baseline_results_bare.json`.
- Training happens in `takemeter.ipynb`, run cell by cell in VS Code with the `.venv` kernel. `nbconvert` isn't installed, so it can't run headless from the shell.
- To dry-run the notebook without real data, set `CSV = "data/practice_labels.csv"` in section 2. That file has 60 posts labelled `analysis` / `hot_take` / `reaction`.

## How the pieces connect

The notebook and the two scripts only talk through files in the repo root:

```
labels.csv ──► notebook sections 1–5 ──► results.json, test_split.csv
          └──► notebook section 6 (unit 6) ──► results_three_seeds_{RUN_LABEL}.json
labels.csv + test_split.csv + label_definitions.txt ──► baseline.py ──► baseline_results.json
data/staff_posts.csv + data/staff_labels.csv + my_staff_labels.csv ──► agreement.py ──► agreement_results.json
```

These have to line up across files. A mismatch fails late or quietly:

- **Label spelling.** The `label` column in `labels.csv`, `LABELS` in notebook section 2, and the keys in `label_definitions.txt` must match exactly, including case. `baseline.py` takes its label list from `labels.csv` and looks each one up in the definitions. A misspelled key silently falls back to the bare label name, which is a weaker prompt.
- **results.json shape.** `baseline.py --trained` reads `accuracy`, `f1_macro`, and `per_label[label].f1`. That is the shape `evaluate()` in notebook section 5 returns, so keep it if you edit `evaluate()`.
- **Staff set matching.** `agreement.py` joins staff posts to staff labels on `id`. It then matches the student's file on text, after collapsing whitespace. Posts have to be pasted unchanged.

Notebook details:

- Earlier cells define functions that later cells reuse: `check_dataset`, `split_dataset`, `train_model`, and `evaluate`. Later cells also rely on globals set earlier: `df`, `LABELS`, `MAX_LENGTH`, `DEVICE`, and the training settings. Sections 6–7 only work after sections 1–5 have run in the same kernel.
- Sections 4 and 5 train and write files in the same cells that define `train_model` and `evaluate`. So getting the kernel ready for sections 6–7 retrains seed 42 and rewrites `results.json` and `test_split.csv`. If `labels.csv` changed, that replaces the unit 5 evidence and the posts `baseline.py` scores. Check `git diff` on both before committing them.
- Section 7 reads `all_results` from section 6 and prints the confusion matrix for the first seed (42) only.
- `labels.csv` is only read in section 2. New rows mean re-running from section 2.
- The notebook does its own stratified 70/15/15 split from `SEED`. `labels.csv` must stay one unsplit file. Splitting it twice leaks posts between train and test.
- Section 6 retrains with seeds 42, 7, and 2024. Its `RUN_LABEL` has to be changed to `"after"` before the unit 6 improvement re-run. Otherwise it overwrites `results_three_seeds_before.json`.
- Training output goes to `takemeter_out/`, which is gitignored.

Script conventions: each script reconfigures stdout and stderr to UTF-8 at the top, because the Windows console is cp1252 and the scripts print non-ASCII characters. Keep that block in any new script. Heavy imports (pandas, transformers) happen inside functions, with a friendly error if they're missing. Messages are written for students and use `[FIX ME]`, `[look at]`, `[note]`, and `[warn]` prefixes.

## Rules that come from the grading

- **Output files are evidence.** `results.json`, `test_split.csv`, `results_three_seeds_*.json`, `baseline_results.json`, and `agreement_results.json` are left out of `.gitignore` on purpose, because they get committed and graded. Don't ignore them.
- **Commit history is evidence too.** It shows `criteria.md` existed before the results. Never amend, rebase, squash, or force-push, and never delete and recreate the repo. Each unit needs at least four new commits. Commits inherited from the starter are excluded by hash (see .github/MAINTAINERS.md).
- **criteria.md.** Five numbered criteria, each naming a number, written before training. In unit 6, never edit or delete an original criterion. A revision goes underneath it as a "Revised in unit 6" note, and only when the criterion couldn't be measured. Lowering a missed target doesn't count as a revision.
- **labels.csv.** One file with columns `text,label,note`, at least 200 rows, and no label over 70%. Posts are kept whole. In the `note` column, the 20 rows labelled without help are marked `cold`, and AI pre-labelled rows are flagged. The file is saved with every field quoted (pandas `QUOTE_ALL`, `\n` line endings). Keep that style when writing it, so a label change shows up as a small diff.
- **One change in unit 6.** The Milestone 5 improvement is the only change allowed this unit. Labels, the taxonomy (README definitions and `label_definitions.txt`), and criteria stay as they are. Don't edit `labels.csv` or the notebook settings except as that one improvement.
- **What Claude must not do for the student.** Don't label or suggest labels for the 20 cold rows, or for the 30 staff-set posts in unit 6 (`my_staff_labels.csv`). Both measure the student's own judgment. AI pre-labelling of the other rows is allowed, but the student has to check every row and disclose it in the README.
- **The staff set uses the staff rules.** It is labelled under `data/staff_taxonomy.md`, not the student's own taxonomy. `data/staff_labels.csv` is the answer key. The student downloads it into `data/` only after `my_staff_labels.csv` is saved. Don't open or print it before then, because there's no way to unsee it.
- **The baseline prediction comes first.** Milestone 1 wants the student's one-sentence prediction in the README before they see the comparison. Don't run `baseline.py --trained` until it's written.
- **README.** Everything is pasted as text. The confusion matrix goes in as a markdown table, never an image. The unit 6 sections stay empty during unit 5. Any AI help in unit 6, such as spotting patterns in errors, gets disclosed under How I Used AI.