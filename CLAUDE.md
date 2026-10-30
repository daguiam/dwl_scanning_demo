# CLAUDE.md

Guidance for Claude Code when working in this repository (dwl_scanning_demo:
DWL scanning demo). This file is committed.

## CRITICAL: Git Commit Standards
- **NEVER use Co-Authored-By tags in commit messages**
- **NEVER include emoji or "Generated with Claude Code" footer in commits**
- Keep commit messages clean and professional
- Conventional commit format: brief title, optional detailed body
- **NEVER work or commit on `main`.** Each development gets its own feature
  branch (`feat/<topic>`, `fix/<topic>`); if HEAD is `main`, create one
  before the first commit
- Commit every significant development to that feature branch
- Feature branches are merged together into an integration branch in a
  session, tested there, and only then go to `main` (by PR)
- Commit `CLAUDE.md`, `JOURNAL.md`, `FEATURES.md`, `ARCHITECTURE.md`,
  `.claude/settings.json` and `.trail/`. Keep `.claude/settings.local.json`
  local (gitignored).

## CRITICAL: Plan Before Large Work
- Before any large development (a phase, a new module, a multi-file
  refactor), present a step-by-step plan with the open decisions and wait
  for the user's approval before changing code

## CRITICAL: Never Drop Functions Silently
- **NEVER remove existing functions without explicit user approval**
- Each removal must be listed in the plan / journal and approved first
- **NEVER replace sophisticated implementations with simplified versions**
- If unsure about a function's purpose, ASK before removing it

## CRITICAL: SI Units Everywhere
- All values in code, arguments and returns are SI (m, s, rad, ...)
- Only scale (µm, nm, mm) when plotting or printing
- Unit labels in square brackets, IEEE style: `x [µm]`, `Wavelength [nm]`,
  `Phase [rad]` - never parentheses, never a bare unit

## CRITICAL: Code Comments and Docstrings
- Docstrings/comments describe the code only: what it does, args, returns,
  usage. Minimal and straightforward
- **NEVER put refactoring notes, reasoning, history or TODO narratives in
  docstrings or comments.** Those go to `JOURNAL.md` / `FEATURES.md`

## CRITICAL: Docstrings Along the Way
- Every function created or modified gets its Google-style docstring (with
  units, shapes and an Example) in the same commit. No deferred doc pass.

## CRITICAL: Read and Update JOURNAL.md
- **ALWAYS read `JOURNAL.md` completely before starting work**
- It tracks design decisions, current state, and lessons learned
- **ALWAYS update the journal after completing ANY significant work**
- Document: what changed, which files, key decisions, test results
- Keep it concise (~100 lines max) - archive stale entries if needed

## Chat log (user request)
- `dev/CHAT_LOG.md` keeps every Claude message of this project's chats as a
  track record of what was changed. A Stop hook in `.claude/settings.local.json`
  runs `python dev/build_chat_log.py` after every turn (append-only, seen ids in
  `dev/CHAT_LOG.seen`). If the hook is missing, run the script at the end of
  each turn. Never commit the log (it is in gitignored `dev/`).

## ARCHITECTURE.md
- Must be updated whenever module structure, class responsibilities or
  inter-module data flow changes. A stale ARCHITECTURE.md is worse than none.

## User feedback (session start)
- User-reported bugs and features live in GitHub issues labelled `feedback`
  (one checklist issue per area, see `BACKLOG.md`). At the start of every
  session run `gh issue list --label feedback`, read their open checkboxes and
  show the owner a short summary so they can pick one
- Items are numbered per issue ("#19 item 1"); numbers never change, new
  items take the next number
- New feedback: add a checkbox to the matching issue. A PR implementing an
  item says `Part of #N`; tick the item with the PR number when opening it

## FEATURES.md
- Local backlog of feature ideas (one-liners are fine)
- When an idea matures, open a GitHub issue and record the issue number next
  to the entry (two-way tracking)

## IMPORTANT: Avoid Overengineering
- Keep solutions simple and direct (KISS, YAGNI)
- Don't create unnecessary abstractions or helper functions
- Keep the dependency list minimal; everything pip-installable (no conda-only
  or custom-compiled packages)

## IMPORTANT: Tests
- New tests should be broad functional/regression tests, not per-function
  unit tests. Ask before implementing them

## IMPORTANT: Local Tests Stay Narrow, CI Runs the Full Suite
- GitHub CI runs the full suites on every push. Locally, run only the tests
  for the code being changed right now (one test file or `-k` selection),
  never the whole suite
- Commit and push often; let CI catch the rest in the background, then fix
  what it reports. The goal is fast iterations in the local sandboxes

## Documentation Standards (Google style)

```python
def function_name(param1, param2 = default):
    """Brief one-line summary of the function.

    Optional extended description.

    Args:
        param1 (float): Description of first parameter. Include units if
            applicable.
        param2: Description with default value noted.

    Returns:
        np.ndarray: Description of return value. Include shape for arrays.

    Raises:
        ValueError: When parameters are invalid.

    Example:
        >>> ap = function_name(1e-6)
    """
```

Module docstrings: brief description, extended purpose, list of main
components. No type hints in signatures. Put the type in the docstring,
`name (type): ...`, only when it is not obvious from the name/description
(e.g. `np.ndarray`, `str | None`). Mention array shapes
and units. Private functions (`_name`) get simpler docstrings.
