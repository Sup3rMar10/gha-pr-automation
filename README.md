# gha-pr-automation

Two automatic checks on every pull request: **the title must follow `type(scope): summary`**, and **labels are added by size and by the paths touched**. Small things that keep a busy repo tidy without anyone having to remember.

## What ran (2026-10-16)

A throwaway pull request ([#1](../../pull/1), closed without merging) showed all of it:

- Title `wip stuff`: the **title check failed** with a clear message; the label job passed.
- Labels `size/S`, `docs`, `code` were added automatically (the label names did not exist beforehand; GitHub created them).
- After renaming it to `fix(api): add bye helper`, the **`edited` event re-ran the checks** and the title check passed.

## Decisions

- **The title goes through `env:`, never into the script.** A pull request title is attacker-controlled text.
- **`permissions: {}` by default; only the label job gets `pull-requests: write`.** (zizmor flagged the workflow-wide grant.)
- **`edited` is in the trigger list**, otherwise fixing a bad title would not re-run the check.
- **Stale size labels are removed** before the new one is added, since a pull request can grow.
- **Why check titles:** with squash merges the title becomes the commit message, so it feeds changelogs and release tooling.

## Honest limits

- **Fork pull requests** get a read-only token, so the label job would fail on them (not tried). The usual fix, `pull_request_target`, is risky; see `gha-security-hardening`.
- Only the small-size path was seen; the `size/M` and `size/L` thresholds (20 and 200 lines) are arbitrary and were not exercised, nor was removal of a stale size label.
- Labels are hard-coded in the workflow rather than configured in a file.

MIT licensed.
