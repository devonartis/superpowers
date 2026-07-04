---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** If working in an isolated worktree, it should have been created via the `superpowers:using-git-worktrees` skill at execution time.

**Save plans to:** `.plans/YYYY-MM-DD-<feature-name>.html`
- (User preferences for plan location override this default)
- **Plans are HTML, not markdown.** They are read by humans in a browser AND executed by agents.
  Step tracking uses `data-status` attributes instead of markdown checkboxes (see templates below).

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Task Right-Sizing

A task is the smallest unit that carries its own test cycle and is worth a
fresh reviewer's gate. When drawing task boundaries: fold setup,
configuration, scaffolding, and documentation steps into the task whose
deliverable needs them; split only where a reviewer could meaningfully
reject one task while approving its neighbor. Each task ends with an
independently testable deliverable.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan is a single self-contained HTML file that MUST start with this skeleton** (dark,
scannable, no external assets). The `<header>` card carries what the markdown header used to:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>[Feature Name] Implementation Plan</title>
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
  .meta{background:var(--card);border:1px solid var(--line);border-radius:10px;padding:14px 18px;margin:16px 0}
  .task{background:var(--card);border:1px solid var(--line);border-radius:12px;margin:14px 0;overflow:hidden}
  .task>.hd{display:flex;gap:12px;align-items:center;padding:13px 18px;background:var(--card2)}
  .task>.hd h3{margin:0;font-size:16px;flex:1}
  .task .bd{padding:8px 18px 16px}
  ol.steps{margin:8px 0;padding-left:20px}
  ol.steps>li{margin:10px 0}
  ol.steps>li[data-status="done"]{opacity:.55}
  ol.steps>li[data-status="done"]>b:first-child::after{content:" ✓";color:var(--done)}
  .files,.io{font-size:13.5px;color:var(--mut)}
</style>
</head>
<body><div class="wrap">

<h1>[Feature Name] Implementation Plan</h1>
<div class="meta">
  <b>For agentic workers:</b> REQUIRED SUB-SKILL: superpowers:subagent-driven-development
  (recommended) or superpowers:executing-plans, task-by-task. Track steps by flipping
  <code>data-status="todo"</code> → <code>data-status="done"</code> on each step's
  <code>&lt;li&gt;</code> (this replaces markdown checkboxes).<br><br>
  <b>Goal:</b> [One sentence describing what this builds]<br>
  <b>Architecture:</b> [2-3 sentences about approach]<br>
  <b>Tech Stack:</b> [Key technologies/libraries]
</div>

<h2>Global Constraints</h2>
<div class="meta">
[The spec's project-wide requirements — version floors, dependency limits, naming and copy
rules, platform requirements — one line each, exact values copied verbatim from the spec.
Every task's requirements implicitly include this section.]
</div>
```

## Task Structure

Each task is a `.task` card. Steps are `<li data-status="todo">` items — an executor flips the
attribute to `"done"` as it completes each step (grep `data-status="todo"` to find remaining work).
Code goes in `<pre><code>` exactly as it would have gone in fenced blocks — complete, no placeholders.

```html
<div class="task" id="task-N">
  <div class="hd"><h3>Task N: [Component Name]</h3></div>
  <div class="bd">
    <p class="files">
      <b>Files:</b><br>
      Create: <code>exact/path/to/file.py</code><br>
      Modify: <code>exact/path/to/existing.py:123-145</code><br>
      Test: <code>tests/exact/path/to/test.py</code>
    </p>
    <p class="io">
      <b>Interfaces:</b><br>
      Consumes: [what this task uses from earlier tasks — exact signatures]<br>
      Produces: [what later tasks rely on — exact function names, parameter and return types.
      A task's implementer sees only their own task; this block is how they learn the names
      and types neighboring tasks use.]
    </p>
    <ol class="steps">
      <li data-status="todo"><b>Write the failing test</b>
<pre><code>def test_specific_behavior():
    result = function(input)
    assert result == expected</code></pre>
      </li>
      <li data-status="todo"><b>Run test to verify it fails</b><br>
        Run: <code>pytest tests/path/test.py::test_name -v</code><br>
        Expected: FAIL with "function not defined"
      </li>
      <li data-status="todo"><b>Write minimal implementation</b>
<pre><code>def function(input):
    return expected</code></pre>
      </li>
      <li data-status="todo"><b>Run test to verify it passes</b><br>
        Run: <code>pytest tests/path/test.py::test_name -v</code><br>
        Expected: PASS
      </li>
      <li data-status="todo"><b>Commit</b>
<pre><code>git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"</code></pre>
      </li>
    </ol>
  </div>
</div>
```

Close the document after the last task: `</div></body></html>`

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, TDD, frequent commits

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `.plans/<filename>.html`. Two execution options:**

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Fresh subagent per task + two-stage review

**If Inline Execution chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:executing-plans
- Batch execution with checkpoints for review
