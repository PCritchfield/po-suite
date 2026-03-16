# Small Batch & Fast Feedback Standard

## Principle

This standard is rooted in **Liatrio's Small Batch & Fast Feedback principle**: work should be sized so that feedback cycles are fast, reviews are thorough, and integration risk is low.

## Definition: Human-Reviewable in One Sitting

An issue should produce a PR that is **human-reviewable in one sitting**.

**Target: ~500 lines changed maximum.**

"Lines changed" is defined as **additions + modifications + deletions** as reported by `git diff --stat`. This is what a reviewer actually reads when evaluating the change.

## Guideline, Not Gate

This is a **conversational guideline enforced through discussion, not a hard gate**. It exists to ensure:
- Reviewers can understand the full change in context
- Integration feedback arrives quickly
- Risk surface area stays bounded
- Rollback and debugging remain tractable

## Exceeding the Standard

If an issue cannot meet the ~500-line standard, it needs further breakdown via `/po-breakdown` to split the work into smaller, sequenced issues that each hit the target.

## Who References This Standard

- `po-breakdown` — uses this standard to guide issue decomposition
- `po-scope-check` — uses this standard to assess PR size compliance
- `po-refine` — uses this standard to help tighten scope during refinement
