# Superpowers — AGENTS.md

Superpowers is a set of process skills for coding agents: brainstorming,
spec writing, planning, implementation, review, and verification. This
file is the entry point for any AGENTS.md-reading agent (pi.dev and
similar). The skills themselves — not this file — are the source of
truth; when a trigger matches, read the skill's `SKILL.md` and follow it
exactly.

## Skill Triggers

| When... | Read and follow |
| --- | --- |
| Starting any conversation | `skills/using-superpowers/SKILL.md` |
| Before any creative work — creating features, adding functionality, modifying behavior | `skills/brainstorming/SKILL.md` |
| A design doc exists and you need a precise, code-level spec before an implementation plan | `skills/writing-specs/SKILL.md` |
| You have a spec or requirements for a multi-step task, before touching code | `skills/writing-plans/SKILL.md` |
| Starting feature work that needs isolation, or before executing an implementation plan | `skills/using-git-worktrees/SKILL.md` |
| You have a written implementation plan to execute in a separate session with review checkpoints | `skills/executing-plans/SKILL.md` |
| Executing an implementation plan with independent tasks in the current session | `skills/subagent-driven-development/SKILL.md` |
| Facing 2+ independent tasks with no shared state or sequential dependencies | `skills/dispatching-parallel-agents/SKILL.md` |
| Implementing any feature or bugfix, before writing implementation code | `skills/test-driven-development/SKILL.md` |
| Encountering any bug, test failure, or unexpected behavior, before proposing fixes | `skills/systematic-debugging/SKILL.md` |
| Producing live/acceptance test evidence for a feature | `skills/acceptance-testing/SKILL.md` |
| About to claim work is complete, fixed, or passing, before committing or opening a PR | `skills/verification-before-completion/SKILL.md` |
| Completing tasks, implementing major features, or before merging | `skills/requesting-code-review/SKILL.md` |
| Receiving code review feedback, before implementing suggestions | `skills/receiving-code-review/SKILL.md` |
| Implementation is complete and tests pass — deciding how to integrate the work | `skills/finishing-a-development-branch/SKILL.md` |
| Creating new skills, editing existing skills, or verifying skills work before deployment | `skills/writing-skills/SKILL.md` |

## Model Roles

Skills name roles (orchestrator, designer, implementer, reviewer,
verifier), never concrete models. See
`skills/using-superpowers/references/model-roles.md` for the full
vocabulary and harness bindings. In one sentence: a role with no binding
in your harness runs on the session model — the model the user chose
when launching their agent.

## Subagent Degradation

If your harness has no subagent capability, execute delegated steps
inline, in order, in the main loop rather than skipping them.

## Platform Notes

- **Claude Code users:** install this as a plugin instead of vendoring
  this file — see the marketplace at `devonartis/superpowers`. The
  plugin version ritual (marketplace update, plugin update) applies to
  you; this file does not.
- **AGENTS.md consumers:** vendor this repo via git submodule or clone
  and pin a tag or commit. There is no plugin version ritual on this
  side — update by moving your pin.

## Contributing

Contributing to this repo (not just using it)? Read `CLAUDE.md` at the
root first — it has the PR requirements and disclosure rules.
