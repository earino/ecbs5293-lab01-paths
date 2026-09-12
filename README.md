# Lab 1 — The missing file that is not missing

**ECBS5293 — Computing for Analytical Work · Session 1, Block 1**

## Goal

`notebooks/analysis.ipynb` should load this project's sales data and print revenue per region. When you are done, every cell runs top to bottom and the last cell prints four regions with a number each.

## How to run (copy exactly)

In your terminal (Git Bash on Windows, Terminal on macOS), in the folder where you keep course work:

```bash
git clone https://github.com/earino/ecbs5293-lab01-paths.git
cd ecbs5293-lab01-paths
uv sync
```

Then open **this folder** in VS Code (*File → Open Folder…*; if VS Code asks whether you trust the authors, choose **Yes** — in Restricted Mode the kernel picker lists no environments at all), open `notebooks/analysis.ipynb`, and in the **kernel picker** (top right) select the interpreter inside this project's **`.venv/`**. You will learn what all of this means in Session 2. For now, do it exactly.

(Alternative: `uv run jupyter lab` from this folder opens the notebook in the browser with the right kernel already selected.)

## What is broken

The second code cell fails with `FileNotFoundError`. The data file **does exist** in this repository. The notebook is looking in the wrong place — and once you fix that, look again.

## What you need to produce

1. The notebook running top to bottom (Kernel → Restart Kernel and Run All Cells) with the revenue table printed.
2. `DIAGNOSIS.md` filled in — all five parts, with the raw output you ran pasted into part 3.
3. **Explain it to a neighbour**, in the last ten minutes (the slide tells you when): symptom, cause, evidence, change, verification, pointing at your screen, not reading the note. Your neighbour asks the three questions on the slide, then you swap. Unsure, or you two disagree? Hands up, and one of us comes to you first.
4. **Lab checkpoint on Moodle**, before you leave: upload the `DIAGNOSIS.md` from your project folder, with its first line filled in (who you explained to, what you need help with).

## Rules

- Fix the *cause*, not the symptom. Do not hard-code a path to your own machine.
- You may use AI to explain errors and suggest what to inspect. You must be able to explain every change you make — your neighbour will ask you to at the end of the lab, in about a minute, without notes, and staff listen in.

## Hints, if stuck

1. First cell to run whenever a path fails: `import os; os.getcwd()`. Where is the kernel actually standing? (It differs between setups — VS Code may put it at the project folder *or* at `notebooks/`. Finding out is the point.)
2. Resolve `raw/sales.csv` from that folder by hand. Does a `raw/` folder exist *there*? Does it exist anywhere? `ls` from the project folder, then `ls data`.
3. The correct relative path is **different depending on where the kernel stands** — which is exactly why the robust fix anchors to the project root instead of guessing. And once the folder part is right, compare filenames: `ls data/raw`.

## The robust fix (recommended)

The kernel's working directory depends on the tool and its settings — VS Code may put it at the project folder *or* at `notebooks/`. A path that works from one breaks from the other. The fix from the lecture works from both: anchor to the project root in the first code cell, with the imports, then build every path from it, like this:

```python
import pandas as pd
from pathlib import Path

PROJECT_ROOT = Path.cwd().parent if Path.cwd().name == "notebooks" else Path.cwd()
print(PROJECT_ROOT)  # must print your project folder

df = pd.read_csv(PROJECT_ROOT / "data/raw/sales_2024.csv")
```

The anchor knows two positions: the project folder and `notebooks/`. From anywhere else it takes the folder the kernel is in, and the next `read_csv` fails with an absolute path in the error — that path tells you where the kernel really was. Open the project folder in VS Code (*File → Open Folder…*), restart the kernel, and print again.

## Diagnosis note

Write it in `DIAGNOSIS.md` (template provided).

## Stretch task

Add `scripts/check_data.py` that uses the same two-line anchor and prints whether the data file exists. Run it from the project folder, then from `scripts/`. One of them fails — why does the anchor's `notebooks` test not help a script? (Hint: a script knows `__file__`; a notebook does not.) Then `git status` — what does it say?

## Git thread

Before you change anything, run `git status` from the project folder. After your fix, run it again. That is the whole Git lesson for today: the repo can tell you what changed.

## The last ten minutes

Finished or not, at minute 33 you turn to the person next to you (three if the row is odd). One of you explains, about a minute: what failed, why, the evidence that showed you, what you changed, how you know it works. Point at the screen; do not read the note. The other asks:

1. Show me the evidence — the raw output that told you the cause.
2. Why did it fail, not just where?
3. The what-if question on the slide.

Then swap. If either of you is unsure, or you disagree, put a hand up: staff come to you first. Then the answer to the what-if, for everyone. An unfinished repair is explained the same way — what you found so far.

Before you leave: the lab's **checkpoint on Moodle** — upload the `DIAGNOSIS.md` from your project folder with its first line filled in. That is what "complete" means; nobody signs you off.

## If you got lost: how to reset

Both of these **destroy work**. Read before running.

**Discard uncommitted changes (destructive)** — throw away edits and new files; keep your commits:

```bash
git restore --staged --worktree .    # every tracked file back to the last commit, staged or not
git clean -fd                        # and remove new, untracked files
```

> ⚠️ Permanently deletes uncommitted changes — staged or not — and any new untracked files.

**Full reset to the starter state (destructive)** — back to exactly what you cloned; throws away your commits too:

```bash
git reset --hard origin/main
git clean -fdx
```

> ⚠️ Discards your local commits and uncommitted changes. The `-x` also removes ignored files — `output/`, the `.venv/` environment — so the folder truly matches a fresh clone (`uv sync` rebuilds the environment in a minute). Without `-x`, leftover generated files can hide the very failure the lab wants you to meet again.
