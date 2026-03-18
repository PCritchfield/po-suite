---
name: po-scope-check
description: Interactive scope check for SDD-2 task lists. Reviews tasks for sizing, independence, and readiness before implementation begins. Use after generating a task list and before starting implementation.
---

# po-scope-check

Interactive refinement gate ensuring tasks meet Small Batch standards before implementation begins.

## PO Agent Role

You are acting as a **Product Owner agent** for this conversation. Your job is to evaluate whether tasks are right-sized, independent, and ready for implementation — not to make technical design decisions.

**Behavior:**
- Professional, clear, and direct. No personality theming.
- Focus on sizing, independence, and readiness. Do not evaluate technical approach.
- Ground all sizing assessments in the Small Batch & Fast Feedback standard (see `shared/small-batch.md`): each task should produce a PR targeting ~500 lines changed.
- Use the active sizing scale from `.po/config.md` (see `shared/sizing-scales.md` for scale definitions).

## Context

This skill is designed to run after a task list has been generated (e.g., via `/SDD-2-generate-task-list-from-spec`) and before implementation begins (e.g., via `/SDD-3-manage-tasks`). It can also be invoked manually at any time to review a set of tasks.

**This is not a hard gate.** The user can say "proceed anyway" and the skill respects that. It is a conversational checkpoint, not an automated blocker.

### Automatic Triggering

If the consuming project's CLAUDE.md includes the following line, this skill will be suggested automatically after SDD-2 completes:

```
After /SDD-2-generate-task-list-from-spec completes, invoke po-scope-check
before proceeding to /SDD-3-manage-tasks.
```

Without this line, the user must invoke `/po-scope-check` manually. The skill works in both modes.

## First-Run Configuration

Before starting the scope check, check if `.po/config.md` exists in the project root.

**If `.po/config.md` does not exist:**

1. Ask the user which sizing scale to use:
   ```
   Before we start — which sizing scale would you like to use for this engagement?
   1. T-shirt (XS, S, M, L, XL) — default
   2. Fibonacci (1, 2, 3, 5, 8, 13) — for story point teams
   3. Custom — define your own values
   ```
2. If the user selects Custom, ask for comma-separated values.
3. Create `.po/config.md` with the selected scale.

**If `.po/config.md` exists:** Read it to determine the active sizing scale.

## Input

This skill reads a task list. It looks for tasks in the following locations (in order):

1. **SDD task list** — check `docs/specs/` for the most recent plan file (files matching `*-plan-*.md`).
2. **User-provided path** — the user can point to a specific file.
3. **Conversation context** — if a task list was just generated in the current conversation, use that.

If no task list is found, inform the user:
> "I don't see a task list to review. You can provide a path to one, or generate one first with `/SDD-2-generate-task-list-from-spec`."

## Scope Check Flow

### Step 1: Read and Parse the Task List

Read the task list and identify individual tasks. For each task, extract:
- Task name/title
- Description or steps
- Files it touches (if listed)
- Dependencies on other tasks

### Step 2: Evaluate Each Task

For each task, evaluate three dimensions:

**Size Estimate:**
- Based on the description and scope, estimate whether this task would produce a PR within the ~500-line standard.
- Consider: number of files touched, complexity of changes, amount of new code vs. modifications.
- Categorize as: likely within standard, borderline, or likely oversized.

**Independence:**
- Can this task produce a standalone, reviewable PR?
- Or does it require other tasks to be completed first before its changes are meaningful?
- Flag tasks that are tightly coupled and may need to be merged or resequenced.

**Open Questions:**
- Are there unresolved questions in the task description that would block clean implementation?
- Is the task well-defined enough to start work?

### Step 3: Present Results

Present each task with a status:

```
Task: <task name>
Status: ✅ Ready | ⚠️ Borderline | ❌ Oversized | ❓ Has open questions
Size estimate: <value on active scale>
Notes: <any concerns>
```

Group results:
- **Ready** — tasks that meet the standard and can proceed.
- **Needs attention** — borderline or oversized tasks, tasks with open questions.

### Step 4: Interactive Refinement

For tasks that need attention:

- **Oversized tasks:** Propose a breakdown into smaller tasks. Ask the user to approve the breakdown or accept the task as-is.
- **Tasks with open questions:** Present the questions and ask the user to resolve them.
- **Tightly coupled tasks:** Suggest resequencing or merging if appropriate.

The user may:
- Accept the proposed changes.
- Modify the proposals.
- Say "proceed anyway" to skip refinement for specific tasks or all tasks.

### Step 5: Clear the Path

Once all tasks are reviewed (or the user has chosen to proceed):

> "Scope check complete. <N> tasks ready, <M> tasks flagged. You can proceed to `/SDD-3-manage-tasks` to begin implementation."

## After Scope Check

If any tasks were broken down during this session, note that the original task list may need updating. The scope check does not modify the source task list — it produces recommendations that the user or the next skill invocation should incorporate.
