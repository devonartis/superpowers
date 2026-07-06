# Superpowers (devonartis fork)

Superpowers is a complete software development methodology for your coding agents, built on top of a set of composable skills and some initial instructions that make sure your agent uses them.

**This is a maintained fork of [obra/superpowers](https://github.com/obra/superpowers)** by Jesse Vincent / Prime Radiant — read the [original release announcement](https://blog.fsck.com/2025/10/09/superpowers/) for the upstream project's origin story. This fork (`github.com/devonartis/superpowers`) adds a stricter planning/verification pipeline on top of upstream's core skills. See [What This Fork Changes](#what-this-fork-changes) below. Install instructions in this README point at THIS fork, not upstream — if you want vanilla upstream Superpowers, go to the link above.

## Quickstart

Give your agent Superpowers: [Claude Code](#claude-code), [Antigravity](#antigravity), [Codex App](#codex-app), [Codex CLI](#codex-cli), [Cursor](#cursor), [Factory Droid](#factory-droid), [GitHub Copilot CLI](#github-copilot-cli), [Kimi Code](#kimi-code), [OpenCode](#opencode), [Pi](#pi).

## How it works

It starts from the moment you fire up your coding agent. As soon as it sees that you're building something, it *doesn't* just jump into trying to write code. Instead, it steps back and asks you what you're really trying to do.

Once it's teased a spec out of the conversation, it shows it to you in chunks short enough to actually read and digest. In this fork, that flow is split into two documents: a design doc (brainstorming) capturing WHAT and WHY, and a spec (writing-specs) adding the code-level precision layer — exact regions to change, contract deltas, an edge-case table.

After you've signed off on the design and spec, your agent puts together an implementation plan that's clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow. It emphasizes true red/green TDD, YAGNI (You Aren't Gonna Need It), and DRY. In this fork, the plan isn't done when the last task is written — a fresh, independent verifier subagent checks it against the design and spec before execution can begin.

Next up, once you say "go", it launches a *subagent-driven-development* process, having agents work through each engineering task, inspecting and reviewing their work, and continuing forward. It's not uncommon for your agent to work autonomously for a couple hours at a time without deviating from the plan you put together.

There's a bunch more to it, but that's the core of the system. And because the skills trigger automatically, you don't need to do anything special. Your coding agent just has Superpowers.

## What This Fork Changes

Relative to upstream `obra/superpowers`, this fork adds:

- **Human-approval artifacts are HTML, not Markdown.** Design docs, specs, and plans exist to be read and approved by a human. A human reads a styled, dark-theme HTML page dramatically faster than raw markdown, and the things that must stand out — decisions, risks, criteria, warnings — actually stand out. Markdown remains for machine-consumed working artifacts only (user-stories files, evidence files).
- **A fixed artifact layout:** `.plans/designs/` for design docs, `.plans/specs/` for specs, `.plans/implementation/` for plans — all `YYYY-MM-DD-<topic>-<kind>.html`.
- **Two new skills:** `writing-specs` bridges design and plan with exact code regions, contract changes, and an edge-case/risk table. `acceptance-testing` is the methodology for producing live/acceptance test evidence that a non-engineer can read and trust.
- **`writing-plans` opens every plan with a Derived-from block** linking the exact design doc and spec it implements — a plan with no provenance is invalid. Success criteria and user stories are extracted from the design/spec before any task decomposition, and every criterion/story is mapped to at least one task.
- **The Independent Plan Verification gate:** a plan is not complete when the last task is written — a fresh verifier subagent (never the plan's author) checks it against the original design + spec, confirms full coverage, and must return PASS before execution can start. On any gap, the author fixes the plan and re-dispatches the verifier.
- **An independent test-author role.** User stories and acceptance tests are authored from the design doc and spec alone, before implementation begins, by a role that never sees the implementation and never edits its own tests afterward. The implementer may add tests but may never modify, weaken, skip, or delete a test-author-authored test — any change requires reviewer sign-off. Unit tests stay the implementer's, in their own red-green loop.
- **Mandatory red-green TDD with recorded red evidence.** A task isn't done without a test observed to fail (red) before the minimal implementation makes it pass (green) — a test never observed red proves nothing, since it may be passing vacuously.
- **Model-role bindings, not hardcoded model names.** Skills name roles (`designer`, `implementer`, `test-author`, `reviewer`, `verifier`, `orchestrator`), never concrete models, so the same skills work across any coding agent. On Claude Code this fork binds: designer → opus, implementer → sonnet, test-author → sonnet (provisional), reviewer → sonnet, verifier → haiku. Elsewhere, an unbound role resolves to the session model. See `skills/using-superpowers/references/model-roles.md`.
- **An `AGENTS.md` entry point** for non-Claude-Code, non-native-discovery harnesses (see [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery)).

## Installation

Installation differs by harness. If you use more than one, install Superpowers separately for each one.

**Two paths:**

- **Claude Code** (and the other harnesses below with a native plugin/package manager) — install via that harness's marketplace or package manager. See the per-harness sections below.
- **pi.dev and other AGENTS.md agents** — pi.dev discovers skills natively (a clone into the right skills folder is enough, see [Pi](#pi) below); harnesses without native discovery vendor this repo and wire it up via `AGENTS.md` (see [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) below).

### Claude Code

Install this fork's marketplace (the plugin is still named `superpowers`, so every `superpowers:*` skill reference in this repo and in your own prompts works unchanged):

```bash
claude plugin marketplace add devonartis/superpowers
claude plugin install superpowers@superpowers-dev
```

**Updating:**

```bash
claude plugin marketplace update superpowers-dev
claude plugin update superpowers
```

Restart Claude Code after updating for the new version to take effect.

> Installing from the official Anthropic marketplace (`/plugin install superpowers@claude-plugins-official`) or from `obra/superpowers-marketplace` gets you **upstream** Superpowers, not this fork's changes above. Use the commands above if you want this fork.

### Antigravity

Install Superpowers as a plugin from this fork's repository:

```bash
agy plugin install https://github.com/devonartis/superpowers
```

Antigravity runs the plugin's session-start hook, so Superpowers is active from
the first message. Reinstall with the same command to update.

### Codex App

The official Codex plugin marketplace (`github.com/openai/plugins`) only carries **upstream** Superpowers — installing "Superpowers" from there will not include this fork's changes. There is no fork-specific Codex App install path today; use the [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) path below (vendor this repo, wire it up via `AGENTS.md`) if you want this fork's behavior in Codex App.

### Codex CLI

Same caveat as Codex App: the built-in `/plugins` marketplace search only surfaces upstream. To use this fork in Codex CLI, vendor it via the [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) path.

### Cursor

Cursor's plugin marketplace search only surfaces upstream Superpowers. To use this fork, vendor it via the [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) path.

### Factory Droid

- Register this fork's repository as the marketplace:

  ```bash
  droid plugin marketplace add https://github.com/devonartis/superpowers
  ```

- Install the plugin:

  ```bash
  droid plugin install superpowers@superpowers
  ```

### GitHub Copilot CLI

Copilot CLI's built-in marketplace command (`copilot plugin marketplace add obra/superpowers-marketplace`) only carries upstream. To use this fork, vendor it via the [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) path.

### Kimi Code

Kimi Code's plugin marketplace only carries upstream Superpowers. You can still install this fork directly from its repository:

```text
/plugins install https://github.com/devonartis/superpowers
```

### OpenCode

OpenCode uses its own plugin install; install Superpowers separately even if you
already use it in another harness. This fork does not maintain a separate OpenCode install script — use the [AGENTS.md-Only Agents](#agentsmd-only-agents-no-native-discovery) path to vendor this repo instead.

### Pi

pi.dev discovers skills natively and recursively, from two locations:

- `~/.pi/agent/skills/` — global, available in every project
- `.pi/skills/` — project-local, walking up from your working directory

Skills use the same `SKILL.md` format pi.dev already follows (frontmatter
`name` + `description` — the Agent Skills standard shared with Claude
Code). Two ways to install, either pinned to a tag or commit:

**Option A — Pi's package manager** (handles placement for you):

```bash
pi install git:github.com/devonartis/superpowers
```

**Option B — manual clone** into one of the native skill locations above:

```bash
# Available in every project:
git clone https://github.com/devonartis/superpowers ~/.pi/agent/skills/superpowers

# Or, project-only:
git clone https://github.com/devonartis/superpowers <project>/.pi/skills/superpowers
```

For local development, run Pi with this checkout loaded as a temporary package:

```bash
pi -e /path/to/superpowers
```

Pi has native skills, so no compatibility `Skill` tool is required.
Subagent and task-list tools remain optional Pi companion packages — core
pi ships without a subagent tool by default, so execute delegated steps
inline, in order, in the main loop if none is installed.

**Triggering:** pi's own docs note that auto-matching skills by
description alone is unreliable. Invoke a skill explicitly with
`/skill:<name>` (e.g. `/skill:brainstorming`), or add a line to your
project's `AGENTS.md` telling the agent to check this repo's `AGENTS.md`
trigger table before starting work — pi concatenates `AGENTS.md` from
`~/.pi/agent/`, parent directories, and your working directory into the
system prompt, so that line is guaranteed to be seen.

**Model roles:** pi users pick the model per session (`/model`, `Ctrl+L`,
or the `--provider`/`--model` flags). With no role bindings, every role
runs on that session model — see
`skills/using-superpowers/references/model-roles.md`. Works with any
Anthropic API model; no Fable, no Claude Code required.

**Name translation:** skill cross-references written as
`superpowers:<name>` are the Claude Code plugin namespace. Pi has no
plugin namespacing — use the bare name, e.g. `/skill:writing-plans`.

**Sources:** [pi skills docs](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/docs/skills.md), [pi coding-agent README](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/README.md)

### AGENTS.md-Only Agents (No Native Discovery)

For AGENTS.md-reading harnesses that don't discover skills natively,
vendor this repo instead (git submodule or clone, pinned to a tag or
commit) and add a line to your project's `AGENTS.md` pointing at the
vendored copy:

```markdown
## Process skills (superpowers)
Read `.agents/superpowers/AGENTS.md` now — it indexes the process skills in
`.agents/superpowers/skills/`. When a task matches a skill's trigger, read
that skill's SKILL.md at its full path and follow it exactly.
```

Vendor `https://github.com/devonartis/superpowers` (not `obra/superpowers`) if you want this fork's changes.

## The Basic Workflow

1. **brainstorming** - Activates before writing code. Refines rough ideas through questions, explores alternatives, presents design in sections for validation. Saves design document to `.plans/designs/`.

2. **using-git-worktrees** - Activates after design approval. Creates isolated workspace on new branch, runs project setup, verifies clean test baseline.

3. **writing-specs** - Activates with an approved design that touches real code regions or contracts. Bridges design and plan: exact code regions staged, contract/schema deltas, an edge-case/risk table. Saves spec to `.plans/specs/`.

4. **writing-plans** - Activates with an approved design (and spec, where one exists). Every plan opens with a Derived-from block, extracts success criteria and user stories before decomposing tasks, and breaks work into bite-sized tasks (2-5 minutes each) with exact file paths, complete code, verification steps. Does not complete until a fresh, independent verifier subagent confirms full design/spec coverage (the Independent Plan Verification gate).

5. **acceptance-testing** - An independent test-author role authors user stories and acceptance tests from the design doc and spec alone, before implementation begins — never from the implementer's own code.

6. **subagent-driven-development** or **executing-plans** - Activates with a verified plan. Dispatches fresh subagent per task with two-stage review (spec compliance, then code quality), or executes in batches with human checkpoints.

7. **test-driven-development** - Activates during implementation. Enforces RED-GREEN-REFACTOR: write failing test, watch it fail (red evidence recorded), write minimal code, watch it pass, commit. Deletes code written before tests. The implementer may add tests but never modifies a test-author-authored test.

8. **requesting-code-review** - Activates between tasks. Reviews against plan, reports issues by severity. Critical issues block progress.

9. **finishing-a-development-branch** - Activates when tasks complete. Verifies tests, presents options (merge/PR/keep/discard), cleans up worktree.

**The agent checks for relevant skills before any task.** Mandatory workflows, not suggestions.

## What's Inside

### Skills Library

**Testing**
- **test-driven-development** - RED-GREEN-REFACTOR cycle with mandatory red evidence (includes testing anti-patterns reference)
- **acceptance-testing** - Independent test-author role produces live/acceptance evidence a non-engineer can read and trust

**Debugging**
- **systematic-debugging** - 4-phase root cause process (includes root-cause-tracing, defense-in-depth, condition-based-waiting techniques)
- **verification-before-completion** - Ensure it's actually fixed

**Collaboration**
- **brainstorming** - Socratic design refinement
- **writing-specs** - Code-level precision layer between design and plan
- **writing-plans** - Detailed implementation plans with a mandatory Independent Plan Verification gate
- **executing-plans** - Batch execution with checkpoints
- **dispatching-parallel-agents** - Concurrent subagent workflows
- **requesting-code-review** - Pre-review checklist
- **receiving-code-review** - Responding to feedback
- **using-git-worktrees** - Parallel development branches
- **finishing-a-development-branch** - Merge/PR decision workflow
- **subagent-driven-development** - Fast iteration with two-stage review (spec compliance, then code quality)

**Meta**
- **writing-skills** - Create new skills following best practices (includes testing methodology)
- **using-superpowers** - Introduction to the skills system

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success

This fork adds one more: **don't grade your own homework** — plans are verified by a fresh subagent, acceptance tests are authored by a role that never saw the implementation.

Read [the original release announcement](https://blog.fsck.com/2025/10/09/superpowers/) for upstream's origin story.

## Contributing

Contributions to **this fork** go through `github.com/devonartis/superpowers` issues and PRs. Fork behavior (HTML artifacts, `writing-specs`/`acceptance-testing`, the plan-verification gate, the test-author role, model-role bindings) is maintained there, independent of upstream's contribution process.

If you're contributing a change to a skill shared with upstream, keep in mind that any updates must still work across all of the coding agents this fork supports:

1. Fork this repository
2. Create a branch for your work
3. Follow the `writing-skills` skill for creating and testing new and modified skills
4. Submit a PR

See `skills/writing-skills/SKILL.md` for the complete guide on skill authoring and testing.

## Updating

Superpowers updates are somewhat coding-agent dependent, but are often automatic. On Claude Code, see the [Updating](#claude-code) command under Installation above.

## License

MIT License - see LICENSE file for details

## Upstream & Community

This fork tracks a subset of upstream's roadmap but is maintained independently. For upstream's own community, commercial support, and telemetry programs (not affiliated with this fork), see:

- Upstream project: [github.com/obra/superpowers](https://github.com/obra/superpowers), built by [Jesse Vincent](https://blog.fsck.com) and [Prime Radiant](https://primeradiant.com)
- Upstream Discord: [Join](https://discord.gg/35wsABTejz) for community support around the base Superpowers project
- Upstream issues: https://github.com/obra/superpowers/issues (for issues in **this fork**, use https://github.com/devonartis/superpowers/issues)
- Upstream release announcements: [Sign up](https://primeradiant.com/superpowers/)
