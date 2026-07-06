# Model Roles

Skills never name concrete models. They name roles. This file maps roles
to models per harness — the indirection layer that keeps skills portable
across any coding agent, on any model, via any API.

## The Resolution Rule

**A role with no binding in your harness resolves to the session model —
the model the user chose when launching their agent. A harness with one
model runs every role on it; that is correct behavior, not a degraded
mode.**

Skills that say "dispatch an implementer-role subagent" or "have a
reviewer-role pass look at this" are naming a role, not a model. Resolve
the role using the bindings below if your harness has them; otherwise the
role runs on whatever model is already running the session.

## Role Vocabulary

| Role | What it does |
| --- | --- |
| **orchestrator** | Plans, decomposes work, dispatches other roles, reviews their output, decides what happens next |
| **designer** | Brainstorming, design, spec authoring, plan authoring — creative and architectural judgment |
| **implementer** | Writes code from a plan or task brief |
| **test-author** | Authors contract tests (user stories, acceptance tests) from the design doc and spec, independent of the implementer |
| **reviewer** | Reviews code or spec/plan documents for correctness and quality |
| **verifier** | Checklist-style verification of another agent's output — cheap/fast tier, narrow scope |

## Claude Code Bindings

The only bindings this fork ships:

| Role | Model |
| --- | --- |
| designer | opus |
| implementer | sonnet |
| test-author | sonnet (PROVISIONAL — owner may rebind) |
| reviewer | sonnet |
| verifier | haiku |
| orchestrator | (session model) |

If the session model is Fable 5, the user's own harness configuration
governs its delegation behavior — this fork takes no position on Fable.

## API-Only / Non-Claude-Code Harnesses

These bindings are optional overrides, not a requirement. If your harness
can invoke different models per task through the API, you MAY bind roles
to different models — for example, verifier to a small/cheap model. If it
can't (or you'd rather not), ignore bindings entirely: every role
resolves to the session model, per the Resolution Rule above.

## Subagent Degradation

If your harness has no subagent capability, execute delegated steps
inline, in order, in the main loop. A role assignment is a note about who
would do this work if delegation were available — it is not a requirement
to fabricate a subagent call your harness can't make.
