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

**Save specs to:** `.plans/specs/YYYY-MM-DD-<topic>-spec.html`
- (User preferences for spec location override this default.)
- The spec sits alongside its design doc (`.plans/specs/YYYY-MM-DD-<topic>-design.html`).

> **⬛ Human-approval artifacts are HTML.** Design docs, specs, and plans exist to be
> READ AND APPROVED BY A HUMAN. A human reads a styled HTML page dramatically faster than
> raw markdown, and the things that must stand out — decisions, risks, criteria, warnings —
> actually stand out. Author them as single self-contained dark-theme HTML files. Markdown
> is for machine-consumed working artifacts (user-stories files, evidence files) only.

The spec uses the same skeleton the brainstorming design template and the writing-plans
plan template use, so design, spec, and plan read as one system.

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

- The template uses `{{PLACEHOLDER}}` slots — replace EVERY `{{...}}` with real content. A
  finished spec contains zero `{{}}` tokens.
- Blocks marked `<!-- repeat -->` are repeatable: duplicate them as many times as the spec
  needs (one code-region subsection per file, one row per edge case) and delete the markers.
- The metadata header is updatable across the spec's life: every field except `created` is
  an append-only comma-separated list — append new entries, never overwrite existing ones.
- Never restate the design's success criteria or user stories; reference and refine only.

## Spec Template

Build the spec from this template. It shares the dark, self-contained skeleton the
brainstorming design template and the writing-plans plan template use (same `:root`
palette, single inline `<style>`, no external assets), so design, spec, and plan read as
one system. Code excerpts and contract JSON live in `<pre><code>`.

**Use the skeleton's emphasis styles deliberately.** Contract changes, edge-case risks, and
the "lives in the design doc" reference notes get visually distinct treatment (the `.note`
callouts, the risks `<table>`) — never buried in body prose. That's the point of HTML: what
must stand out, stands out.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Spec: {{TITLE}}</title>
<style>
  :root{--bg:#0f1115;--card:#181b22;--card2:#1e222b;--line:#2a2f3a;--fg:#e6e9ef;--mut:#9aa4b2;
        --todo:#3b82f6;--done:#22c55e;--accent:#38bdf8;}
  body{margin:0;background:var(--bg);color:var(--fg);
       font:15px/1.6 -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;}
  .wrap{max-width:940px;margin:0 auto;padding:32px 24px 80px}
  code,pre{background:#20242d;border-radius:4px;font:12.5px/1.5 ui-monospace,Menlo,monospace;color:#cfe3ff}
  code{padding:1px 6px} pre{padding:12px;overflow-x:auto} pre code{padding:0;background:none}
  h1{font-size:26px;margin:0 0 6px}
  h2{font-size:16px;text-transform:uppercase;letter-spacing:.03em;color:var(--mut);
     margin:34px 0 12px;padding-bottom:6px;border-bottom:1px solid var(--line)}
  h3{font-size:15px;margin:20px 0 6px}
  p{margin:8px 0}
  .meta{background:var(--card);border:1px solid var(--line);border-radius:10px;padding:14px 18px;margin:16px 0}
  .meta summary{cursor:pointer;color:var(--mut);font-weight:600}
  .meta dl{display:grid;grid-template-columns:max-content 1fr;gap:4px 16px;margin:12px 0 0}
  .meta dt{color:var(--mut)} .meta dd{margin:0}
  .note{background:var(--card);border-left:3px solid var(--accent);border-radius:6px;
        padding:10px 14px;margin:10px 0;color:var(--mut)}
  table{border-collapse:collapse;width:100%;margin:8px 0;font-size:13.5px}
  th,td{border:1px solid var(--line);padding:8px 10px;text-align:left;vertical-align:top}
  th{background:var(--card2);color:var(--mut);text-transform:uppercase;letter-spacing:.03em;font-size:12px}
</style>
</head>
<body><div class="wrap">

<!-- ===== HEADER + FRONT MATTER + UPDATABLE METADATA ===== -->
<h1>Spec: {{TITLE}} — {{SHORT_DESCRIPTION}}</h1>
<div class="meta">
  <b>Status:</b> {{STATUS: Spec | In Progress | Complete}}<br>
  <b>Priority:</b> {{PRIORITY: P0/P1/P2}} — {{PRIORITY_JUSTIFICATION}}<br>
  <b>Effort estimate:</b> {{EFFORT}}<br>
  <b>Depends on:</b> {{DEPENDENCIES: what must be done first}}<br>
  <b>Design doc:</b> <code>{{DESIGN_DOC_PATH: e.g. .plans/specs/YYYY-MM-DD-TOPIC-design.html}}</code><br>
  <b>Tech debt:</b> {{TRACKER_REF: if applicable}}
</div>

<!-- Updatable metadata. Every field except `created` is an append-only comma-separated
     list — append new entries across the spec's life, never overwrite existing ones. -->
<details class="meta">
  <summary>Metadata</summary>
  <dl>
    <dt>created</dt>      <dd>{{CREATED_ISO}}</dd>
    <dt>modified</dt>     <dd>{{MODIFIED_ISO_LIST}}</dd>
    <dt>commits</dt>      <dd>{{COMMIT_SHA_LIST}}</dd>
    <dt>agents</dt>       <dd>{{AGENT_NAME_LIST}}</dd>
    <dt>sessions</dt>     <dd>{{SESSION_ID_LIST}}</dd>
    <dt>back refs</dt>    <dd>{{BACK_REFERENCES: e.g. the design doc this spec implements}}</dd>
    <dt>forward refs</dt> <dd>{{FORWARD_REFERENCES: e.g. the plan built from this spec}}</dd>
  </dl>
</details>

<!-- ===== OVERVIEW ===== -->
<h2>Overview</h2>
<p>{{OVERVIEW: narrative — what, why, and context. Tell the story so someone who missed
   the last three sessions understands. Include the problem statement: what's broken,
   missing, or insufficient today. Reference specific code, config, or behavior.}}</p>
<p><b>What changes:</b> {{WHAT_CHANGES: one paragraph listing all modifications.}}</p>
<p><b>What stays the same:</b> {{WHAT_STAYS: one paragraph confirming what is NOT touched.}}</p>

<!-- ===== GOALS & SUCCESS CRITERIA ===== -->
<h2>Goals &amp; Success Criteria</h2>
<p class="note"><b>Success criteria live in the design doc</b> (linked above). Do NOT
   restate them here — link to the design and list ONLY refinements: new testable outcomes
   discovered while writing this spec. If a criterion changes, change it in the design (its
   Amendments section) and reference it here.</p>
<p>{{SUCCESS_CRITERIA_REFINEMENTS: link to the design's criteria, plus refinements only.}}</p>

<!-- ===== NON-GOALS ===== -->
<h2>Non-Goals</h2>
<ol>
  <!-- repeat -->
  <li>{{NON_GOAL: what this spec explicitly does NOT do, with where/when it's addressed}}</li>
</ol>

<!-- ===== USER STORIES ===== -->
<h2>User Stories</h2>
<p class="note"><b>User stories live in the design doc.</b> Link to the design's User
   Stories section. Add here ONLY stories discovered during spec work; move them into the
   design via its Amendments section if they change scope.</p>
<p>{{USER_STORY_ADDITIONS: link to the design's stories, plus additions discovered here.}}</p>

<!-- ===== CONTRACT CHANGES ===== -->
<h2>Contract Changes</h2>
<p><b>Schema:</b> exact schema/migration for any data-store changes, or "None — no schema changes."</p>
<pre><code>{{SCHEMA_OR_NONE}}</code></pre>
<p><b>API:</b> request/response examples for new/changed endpoints or interfaces, or
   "None — no contract changes." Include error responses if applicable.</p>
<pre><code>{{API_OR_NONE}}</code></pre>

<!-- ===== CODEBASE CONTEXT & CHANGES ===== -->
<h2>Codebase Context &amp; Changes</h2>
<p class="note"><b>The spec author already read these files.</b> Capture the exact code
   sections here so the planning agent (writing-plans) does NOT need to re-read them. Each
   subsection is one file region: what it does today, what needs to change, and why.</p>

<!-- repeat: one <h3> + code + change per file region -->
<h3><code>{{FILE_PATH}}:{{NN-MM}}</code> — {{WHAT_THIS_SECTION_DOES}}</h3>
<pre><code>{{EXACT_CODE_TO_MODIFY}}</code></pre>
<p><b>Change:</b> {{CHANGE: enough detail for a coding agent to implement without guessing.}}</p>

<!-- ===== EDGE CASES & RISKS ===== -->
<h2>Edge Cases &amp; Risks</h2>
<p>Include race conditions, failure modes, concurrency, config mistakes, backward
   compatibility, and rollback — all in one table.</p>
<table>
  <thead><tr><th>Case</th><th>What Happens</th><th>Mitigation</th></tr></thead>
  <tbody>
    <!-- repeat: one row per case -->
    <tr><td>{{SCENARIO}}</td><td>{{CONSEQUENCE}}</td><td>{{MITIGATION}}</td></tr>
  </tbody>
</table>

<!-- ===== ACCEPTANCE TESTING ===== -->
<h2>Acceptance Testing</h2>
<p class="note"><b>Before writing any test code</b>, extract the user stories from the
   design doc's User Stories section (plus any additions above) into a standalone Markdown
   file: <code>tests/{{PHASE_OR_FIX}}/user-stories.md</code>, then follow the
   acceptance-testing skill. The stories ARE the acceptance criteria — if they can't be
   written, the spec isn't clear enough to implement.<br>
   <b>Required skill:</b> <code>superpowers:acceptance-testing</code></p>

<!-- ===== IMPLEMENTATION PLAN ===== -->
<h2>Implementation Plan</h2>
<p class="note"><b>After acceptance stories are written</b>, create the implementation plan
   with the writing-plans skill.<br>
   <b>Required skill:</b> <code>superpowers:writing-plans</code><br>
   <b>Save to:</b> <code>.plans/YYYY-MM-DD-{{TOPIC}}-plan.html</code><br>
   The plan header must reference this spec
   (<code>.plans/specs/YYYY-MM-DD-{{TOPIC}}-spec.html</code>), and each task maps to one or
   more acceptance stories from <code>tests/{{PHASE_OR_FIX}}/user-stories.md</code>. The
   plan is the bridge between "what to build" (this spec) and "how to build it" (TDD tasks).
   Do not skip it.</p>

</div></body></html>
```

## Self-Review

After writing the spec, look at it against the design doc with fresh eyes:

1. **Reference discipline:** Did you restate any success criterion or user story that
   belongs in the design? Replace it with a link + refinement.
2. **Placeholder scan:** Any leftover `{{...}}` tokens, "TBD", or empty sections? Fill them.
   A finished spec contains zero `{{}}` tokens.
3. **Region completeness:** Does every file a task will touch appear in Codebase Context
   with its exact current code and a stated Change? The planning agent reads only this.
4. **Contract precision:** Are schema/API deltas exact enough to implement without guessing?

Fix issues inline. No need to re-review — just fix and move on.

## Handoff

The spec is done when its Codebase Context, Contract Changes, and Edge Cases sections are
complete and its Goals/User Stories sections point back to the design. Next steps, in order:

1. Write the acceptance stories — `superpowers:acceptance-testing`.
2. Write the implementation plan — `superpowers:writing-plans`.
