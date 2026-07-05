---
name: writing-specs
description: Use when a design doc exists and you need a precise, code-level spec before writing an implementation plan — the bridge between design and plan
---

# Writing Specs

## Position in the Flow

**brainstorming (design doc) → writing-specs (this skill) → writing-plans (plan).**

A design doc says WHAT and WHY: it carries the Purpose/Problem/Solution, the
**success criteria**, and the **user stories**. A spec adds the precision layer the
planning agent needs to work without re-deriving anything:

- the **exact code regions** staged for the planning agent — read once, captured here
- **contract changes** — schema and API/interface deltas, spelled out
- an **edge-case / risk table** — failure modes, backward-compat, rollback

The spec **references** the design's success criteria and user stories — it never
restates them. One home per fact, no drift. If a criterion or story changes, change it
in the design doc (its Amendments section) and reference it here.

**Announce at start:** "I'm using the writing-specs skill to write the spec."

**Save specs to:** `.plans/specs/YYYY-MM-DD-<topic>-spec.md`
- (User preferences for spec location override this default.)
- The spec sits alongside its design doc (`.plans/specs/YYYY-MM-DD-<topic>-design.html`).

## When to Write a Spec

A spec is worth writing when the work is code-level and a separate planning agent will
turn it into tasks — the spec is what lets that agent skip re-reading the codebase.

- **You have an approved design doc.** The spec implements it; it does not replace it.
- **The change touches real code regions or contracts.** If there is nothing to stage
  (exact files, schema, API), the design may be enough — go straight to writing-plans.
- **Scope is a single plan.** If the design still covers multiple independent subsystems,
  decompose first; write one spec per subsystem.

## What a Spec Adds — and What It Must Not Restate

| Section | Rule |
|---------|------|
| Success criteria | **Live in the design doc.** Link to it; list ONLY refinements discovered while writing the spec. |
| User stories | **Live in the design doc.** Link to it; add ONLY stories discovered during spec work. |
| Contract changes | Exact schema and API/interface deltas, with request/response and error examples. |
| Codebase context | The exact code regions to change — pasted so writing-plans never re-reads them. |
| Edge cases & risks | One table: race conditions, failure modes, config mistakes, backward-compat, rollback. |

## Template Discipline

- The template uses `[...]` bracket slots — replace EVERY slot with real content. A
  finished spec contains no leftover `[...]` placeholder text.
- Repeatable blocks (numbered code-region subsections, table rows) are duplicated as many
  times as the spec needs.
- Never restate the design's success criteria or user stories; reference and refine only.

## Spec Template

Build the spec from this template (Markdown — specs are code-and-table heavy reference
documents; the design doc and plan carry the HTML visual layer).

````markdown
# [Title]: [Short Description]

**Status:** Spec | In Progress | Complete
**Priority:** P0/P1/P2 — [one-line justification]
**Effort estimate:** [time estimate]
**Depends on:** [what must be done first]
**Design doc:** [path to the design doc this spec implements — e.g. .plans/specs/YYYY-MM-DD-<topic>-design.html]
**Tech debt:** [tracker reference if applicable]

---

## Overview

[Narrative — what, why, and context. Tell the story so someone who missed the
last three sessions understands. Include the problem statement: what's broken,
missing, or insufficient today. Reference specific code, config, or behavior.]

**What changes:** [One paragraph listing all modifications.]

**What stays the same:** [One paragraph confirming what is NOT touched.]

---

## Goals & Success Criteria

> **Success criteria live in the design doc** (linked above). Do NOT restate them
> here — link to the design and list ONLY refinements: new testable outcomes
> discovered while writing this spec. If a criterion changes, change it in the
> design (its Amendments section) and reference it here.

---

## Non-Goals

1. [What this spec explicitly does NOT do, with where/when it will be addressed]

---

## User Stories

> **User stories live in the design doc.** Link to the design's User Stories
> section. Add here ONLY stories discovered during spec work; move them into the
> design via its Amendments section if they change scope.

---

## Contract Changes

**Schema:** [Exact schema/migration for any data-store changes, or "None — no schema changes."]

**API:** [Request/response examples for new/changed endpoints or interfaces, or
"None — no contract changes." Include error responses if applicable.]

---

## Codebase Context & Changes

> **The spec author already read these files.** Capture the exact code sections
> here so the planning agent (writing-plans) does NOT need to re-read them. Each
> subsection is one file region: what it does today, what needs to change, and why.

### 1. `path/to/file.ext:NN-MM` — [What this section does]

```
// Paste the exact code that will be modified.
```

**Change:** [What to do — enough detail for a coding agent to implement
without guessing.]

### 2. `path/to/another-file.ext:NN-MM` — [Description]

```
// Same pattern. One subsection per file or code region.
```

**Change:** [What to do.]

---

## Edge Cases & Risks

| Case | What Happens | Mitigation |
|------|-------------|------------|
| [Scenario] | [Consequence] | [How we handle it] |
| [Backward-compat issue] | [Impact] | [Migration path or "automatic"] |
| [Rollback scenario] | [Data safety] | [Step-by-step rollback] |

[Include: race conditions, failure modes, concurrency, config mistakes,
backward compatibility, and rollback — all in one table.]

---

## Acceptance Testing

> **Before writing any test code**, extract the user stories from the design
> doc's User Stories section (plus any additions above) into a standalone file:
> `tests/<phase-or-fix>/user-stories.md`, then follow the acceptance-testing
> skill. The stories ARE the acceptance criteria — if they can't be written, the
> spec isn't clear enough to implement.
>
> **Required skill:** `superpowers:acceptance-testing`

---

## Implementation Plan

> **After acceptance stories are written**, create the implementation plan with
> the writing-plans skill.
>
> **Required skill:** `superpowers:writing-plans`
> **Save to:** `.plans/YYYY-MM-DD-<topic>-plan.html`
>
> The plan header must reference this spec:
> `Spec: .plans/specs/YYYY-MM-DD-<topic>-spec.md`
> Each task maps to one or more acceptance stories from
> `tests/<phase-or-fix>/user-stories.md`.
>
> The plan is the bridge between "what to build" (this spec) and "how to build
> it" (TDD tasks). Do not skip it.
````

## Self-Review

After writing the spec, look at it against the design doc with fresh eyes:

1. **Reference discipline:** Did you restate any success criterion or user story that
   belongs in the design? Replace it with a link + refinement.
2. **Placeholder scan:** Any leftover `[...]` slots, "TBD", or empty sections? Fill them.
3. **Region completeness:** Does every file a task will touch appear in Codebase Context
   with its exact current code and a stated Change? The planning agent reads only this.
4. **Contract precision:** Are schema/API deltas exact enough to implement without guessing?

Fix issues inline. No need to re-review — just fix and move on.

## Handoff

The spec is done when its Codebase Context, Contract Changes, and Edge Cases sections are
complete and its Goals/User Stories sections point back to the design. Next steps, in order:

1. Write the acceptance stories — `superpowers:acceptance-testing`.
2. Write the implementation plan — `superpowers:writing-plans`.
