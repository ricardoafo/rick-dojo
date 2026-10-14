# Dojo rules

The dojo is where the mechanical-dev muscle gets built: small, spec'd exercises written by hand, reviewed like production code. One exercise a week, one evening.

## 1. AI off

- No AI while writing dojo code: no chat assistants, no Copilot, no AI autocomplete in the editor. Turn them off before you start, not when you get stuck.
- Allowed: the Python docs, tool docs (uv, ruff, pyright, pytest), books, Boot.dev material, `help()`, the REPL, your own earlier code.
- Not allowed: copying solution code from anywhere. Reading a doc page about a concept is fine; pasting someone's answer is not.
- AI comes in only after the PR is open, as the reviewer.

## 2. The loop

1. **Spec.** Claude commits `NN-name/SPEC.md` and `NN-name/tests/test_contract.py`. The spec has a goal, constraints and the tests that must pass. Claude never commits solution code.
2. **Branch.** You branch `dojo/NN-name` from `main` and write the solution.
3. **PR.** You open a PR into `main` using the checklist in section 4.
4. **Review.** Claude clones the branch, runs the gates, and reviews like a senior engineer: correctness, tests, design, idioms. Every review ends with two questions you must answer in the PR.
5. **Merge.** You merge only after the gates are green and both questions are answered in writing. If you cannot defend a line, it does not merge.

## 3. Gates

Every exercise is its own `uv` project. From the exercise folder, all four must pass:

```
uv run ruff check
uv run ruff format --check
uv run pyright
uv run pytest
```

- pyright runs in `strict` mode. No `Any`, no `# type: ignore`, no `# pyright: ignore`, no `# noqa` in your code.
- ruff selects at least `E`, `F`, `I`, `B`, `UP`.
- `tests/test_contract.py` is the contract. Never edit it. Your own tests go in a separate file in `tests/`.

## 4. PR checklist

Paste this into every PR description and fill it in:

```
- [ ] AI was off while I wrote this code
- [ ] Gates: ruff check, ruff format --check, pyright, pytest (paste the output)
- Time spent: __ min (timebox: __ min)
- Hardest part:
- What I would do differently:
```

## 5. Timebox and slips

- Each spec states a timebox. When you hit twice the timebox, stop and open the PR with what you have, marked draft. An unfinished PR is evidence; a silent week is a slip.
- An exercise you abandon, or one that needed a hint to finish, gets redone from an empty folder a week later.
- The PR link goes in the Notion Weekly Log. No PR link, no "shipped".

## 6. Conventions

- Python 3.12+, managed with `uv`. Standard library only at runtime unless the spec says otherwise.
- `src/` layout: the package lives in `NN-name/src/<package>/`.
- Exercise folders are numbered `01-`, `02-`, … in the order they were issued.
