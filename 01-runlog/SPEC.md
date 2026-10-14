# Dojo 01: runlog

**Issued:** 2026-10-14 for 2026-W43 (Term 1, week 1) · **Timebox:** 90 min, tooling setup included · **PR due:** before the Sunday review, Oct 25 12:10

## Goal

Build a small, fully typed Python library that parses a test-run log and summarizes it: counts, pass rate, the slowest test, and the flaky tests. It is the week-1 tooling exercise in disguise: you set up a `uv` project with ruff, pyright (strict) and pytest from an empty folder, then make the contract tests pass.

## Input format

Each record is one line with four whitespace-separated fields:

```
2026-10-19T07:01:02Z PASS test_login 132ms
```

| Field | Rule |
| --- | --- |
| timestamp | ISO 8601 with a timezone (`Z` or an offset like `+02:00`). A timestamp without a timezone is invalid. |
| status | Exactly `PASS`, `FAIL` or `SKIP` (upper case). |
| test id | Any non-empty text without whitespace, e.g. `tests/api.py::test_login`. |
| duration | A non-negative whole number followed by `ms`, e.g. `0ms`, `132ms`. |

Fields may be separated by any run of spaces or tabs, and the line may have leading or trailing whitespace (including the newline).

## Public API

Everything below is importable from the `runlog` package.

| Name | What it is |
| --- | --- |
| `Status` | An enum with members `PASS`, `FAIL`, `SKIP`. |
| `RunRecord` | An immutable record with fields `timestamp` (timezone-aware `datetime`), `status` (`Status`), `test_id` (`str`), `duration_ms` (`int`). |
| `ParseError` | The error raised for any invalid input. It is a subclass of `ValueError`. |
| `parse_line(line: str) -> RunRecord` | Parses one record line. Raises `ParseError` for anything that is not a valid record, including blank lines and comments. |
| `RunSummary` | An immutable record with fields `total`, `passed`, `failed`, `skipped` (all `int`), `pass_rate` (`float \| None`), `slowest` (`RunRecord \| None`), `flaky` (`frozenset[str]`). |
| `summarize(lines: Iterable[str]) -> RunSummary` | Summarizes a whole log. |

### `summarize` rules

- Blank lines, and lines whose first non-whitespace character is `#`, are skipped.
- Every other line must be a valid record. If one is not, raise `ParseError` whose message contains `line N`, where N is the 1-based position of that line in the input (blank and comment lines count).
- Counts are per line: a test that ran three times counts three times.
- `pass_rate` is `passed / (passed + failed)`. Skips are excluded. If nothing passed or failed, it is `None`.
- `slowest` is the record with the largest `duration_ms`; on a tie, the first one in the input. `None` for an empty log.
- `flaky` holds every test id that has at least one `PASS` and at least one `FAIL` in the log. `SKIP` never makes a test flaky.
- `lines` can be any iterable of strings, including a generator or an open file. Read it once.

## Constraints

- AI off. See [RULES.md](../RULES.md).
- Python 3.12+, standard library only at runtime. Dev dependencies: pytest, ruff, pyright.
- `src/` layout: `01-runlog/src/runlog/`. The project's `pyproject.toml` lives in `01-runlog/`, and the tests import the installed package.
- All four gates from RULES.md pass from `01-runlog/`, with pyright in strict mode and ruff selecting at least `E`, `F`, `I`, `B`, `UP`.
- Do not edit `tests/test_contract.py`. Add at least three tests of your own, in `tests/test_mine.py`, for cases the contract does not cover. Name each test after the behaviour it pins down.

## Tests that must pass

[`tests/test_contract.py`](tests/test_contract.py): 37 test cases covering valid and invalid lines, immutability, counts, pass rate, slowest, flaky detection, line numbers in errors, and generator and file input. Plus your own tests in `tests/test_mine.py`.

## Concepts to read (not code to copy)

- `dataclasses`: `frozen=True`, and why immutability matters for records.
- `enum.StrEnum` and looking up a member by value.
- `datetime.fromisoformat`, and aware versus naive datetimes.
- Turning a low-level error into your own: `raise ... from ...`.
- `collections.abc.Iterable`, and the fact that a generator can only be consumed once.
- pytest: `parametrize`, `pytest.raises(match=...)`, `pytest.approx`.
- uv: `uv init --lib`, `uv add --dev`; ruff and pyright configuration in `pyproject.toml`.

## Done when

The PR into `main` is open with the RULES.md checklist filled in, all four gates pass on review, and you have answered both review questions.
