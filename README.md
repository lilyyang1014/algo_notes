# Algo Notes

This repository is my personal algorithm notes and mistake log, written while learning data structures and algorithms (mainly LeetCode, Java).

The goal is **not** to collect final answers, but to **track how my understanding evolves over time**.

---

## What this repository is

- A personal **algorithm mistake notebook**
- A place to record:
  - my first intuition
  - wrong assumptions
  - why a solution works (or doesn’t)
  - how my thinking changes after revisiting a problem

This repo is meant for **long-term learning**, not quick reference.

---

## How I use this repo

For each problem:

1. I write down my **initial intuition and attempt**
2. I later add:
   - mistake analysis
   - alternative solutions
   - structural insights
3. I keep **one markdown file per problem**
4. I use **Git commits** to record different stages of understanding

The markdown file always reflects my *current best understanding*,  
while the **commit history records how I got there**.

---

## Versioning philosophy

- Files show the **latest, most complete version**
- Commits represent **snapshots of my thinking**
- I do **not** duplicate files for revisits
- Re-solving a problem results in **new commits**, not new files

Typical commit stages:
- `feat:` initial intuition / first attempt
- `docs:` mistake analysis or refinements
- `docs:` complete solution and insights
- `docs:` revisit notes after re-solving

---

## Directory structure

Problems are organized by **data structure / problem model**, not by solution technique.

```text
algo_notes/
├── tree/
│   ├── LC116.md
│   └── assets/
│       └── 116.png
├── dp/
├── graph/
└── ...
