---
layout: post
title: "Taming noisy syntax diffs — reorder JSON keys to match git"
date: 2026-06-28
---

A small annoyance that surfaces from the deep every time you switch machines or do a fresh
`git clone`: **file creation dates change**. The files are byte-for-byte the same, but the
filesystem stamps them with a new "created" timestamp.

Why does that matter? When 4D builds a project, it generates
`Resources/en.lproj/syntaxEN.json` — and the **order of keys** in that file follows the
creation order of your source files. New machine, new checkout, new creation dates → the keys
come out in a different order. The content is identical, but git sees a giant reshuffled diff.
Noise. You can't tell whether anything *actually* changed.

## The fix: reorder keys to match HEAD

The trick is to put the keys back in the order git already knows about. I keep a small Python
script in a [gist](https://gist.github.com/mesopelagique/71859601082fb5adb47f06ac1b8dcbe0) for
exactly this. It looks at the JSON files you've modified (`git diff --name-only HEAD`), reads
the original key order from `HEAD`, and rewrites each file so that:

- keys that already existed in the original come first, **in their original order**,
- any genuinely new keys are appended after.

It works recursively on nested objects, preserves the file's existing indentation (spaces or
tabs) and trailing newline, and only writes when something actually changed. The result:
reordering-only churn collapses to **zero diff**, and any real change stands out clearly.

## Run it on macOS

From your project folder:

```bash
curl -sL https://gist.githubusercontent.com/mesopelagique/71859601082fb5adb47f06ac1b8dcbe0/raw/d078066c036aa8f0f501b9f511529a09da5094cd/minimize_json_diff.py | python3 -
```

That processes every modified JSON file in the working tree. Two handy options if you pipe it
to a local copy instead:

- `--dry-run` — show what *would* change without touching files,
- a list of file paths — restrict it to specific files instead of everything modified.

## Where it really belongs

Running it by hand works, but the ideal home is **automatic, right after the build regenerates
the file**:

- If 4D exposes a **post-compile hook** you can configure, that's its natural place — reorder
  the freshly written `syntaxEN.json` before it ever reaches git.
- Otherwise a **pre-commit hook** is a solid fallback: it normalizes key order on the way into
  a commit, so the noise never lands in history in the first place.

Either way, the goal is the same — keep diffs honest, so a "no change" really reads as no
change.

---

Find the full 4D component catalog on the [home page](/), and more AI / Swift / 4D notes over
on [phimage.github.io](https://phimage.github.io/).
