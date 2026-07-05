---
name: acceptance-testing
description: Use when producing live/acceptance test evidence for a feature — proving a real user can accomplish a real task against the REAL system, recorded so a non-engineer can read it
---

# Acceptance Testing

Live/acceptance tests prove a **real user can accomplish a real task against the REAL
system**, recorded as evidence a non-engineer can read and trust. They are written after
the spec's user stories exist, and they are how "we think it works" becomes "we proved it
works." This skill is the **methodology**. Each project supplies the specifics.

**Announce at start:** "I'm using the acceptance-testing skill to write the live test evidence."

## Read the Project Overlay FIRST

Each project defines its own overlay — **personas and their tools, deployment modes and
their order, environment setup, and worked examples** — in the project's test template
(e.g. `tests/LIVE-TEST-TEMPLATE.md`) or its testing rules.

**Read the project overlay BEFORE writing any story.** Personas, modes, commands, and
examples defined there OVERRIDE any generic example in this skill. This skill tells you the
shape of good acceptance evidence; the project tells you who the personas are, which
deployment modes run and in what order, and what the real commands look like. If the
project has no overlay, the generic guidance below stands on its own.

## Who Reads the Evidence — Audience First

**Executives and manual QA are the primary readers. The engineer reads last.**

- **An executive** reads the evidence folder to decide whether a release is safe. They need
  to understand what changed, what could go wrong, and whether we proved it works.
- **A QA tester** reads a story to understand what they are verifying. They need to
  reproduce it and write a verdict without understanding the system's internals.
- **An engineer** reads last. If the evidence is clear for the first two audiences, the
  engineer gets what they need automatically.

Write for the executive; the other two follow. **If they must ask an engineer what it
means, the evidence failed.**

## What Is a Live Test?

A person — or an agent acting as a person — running commands against the **real system**
and recording what happened. It is NOT a script, NOT a bash chain, NOT automation.

Run against **compiled / real artifacts only** — the same thing that ships. Never an
interpreted shortcut or a dev-time runner in place of the built artifact, and never a unit
test. The project overlay names the exact artifacts and the deployment modes (and their
order) a story must run in.

## Story Classification

Every story in `user-stories.md` MUST be tagged with exactly one classification. No
untagged stories.

| Tag | Meaning | Gate Question |
|-----|---------|---------------|
| `[PRECONDITION]` | Verifies infrastructure or setup is in place. A smoke test. | "Is this proving a dependency works, not the feature itself?" |
| `[ACCEPTANCE]` | Real-world end-to-end use case with a real consumer. | "Would a real user do this in production?" |

- A `[PRECONDITION]` story checks that a tool, service, or dependency is available. It
  enables acceptance stories but does not prove the feature works.
- An `[ACCEPTANCE]` story proves a real user can accomplish a real task end-to-end. If you
  deleted every `[ACCEPTANCE]` story and kept only preconditions, you'd have zero proof the
  feature works.

**If unsure which a story is, ask:**
> "If this test passes but every other test is deleted, does a real user get value from the
> feature?" — YES → `[ACCEPTANCE]`. NO → `[PRECONDITION]`.

**Minimum bar:** at least one `[ACCEPTANCE]` story MUST involve a **real third-party
consumer** — something outside the system that trusts and uses the system's output. The
system talking to itself is not acceptance.

## Infrastructure Prerequisites

Every `user-stories.md` MUST begin with an Infrastructure Prerequisites table listing
everything that must exist before ANY test can run. Each prerequisite maps to a
`[PRECONDITION]` story that smoke-tests it.

| Prerequisite | Purpose | Smoke Test Story | Status |
|-------------|---------|-----------------|--------|
| [external dependency] | [why the tests need it] | [PRECONDITION story id] | NOT VERIFIED |

- Every external dependency gets a row — anything not the core artifact under test
  (accounts, API keys, third-party tools, language runtimes, packages, tunnels, DNS,
  certificates, etc.).
- Every row maps to a `[PRECONDITION]` story — no prerequisites without a smoke test.
- Status starts `NOT VERIFIED` and becomes `VERIFIED` during preflight, before live tests run.
- **If a prerequisite cannot be verified, live tests STOP.** Tests against missing
  infrastructure are fiction, not tests.
- If a feature needs no external infrastructure, write "None — all tests run against the
  local system." Do NOT omit the section.

## Ground Every Story in Reality

Before writing a story, ask: **"Is this really how this would work in the real world?"**

- Choose the persona that would actually perform the action in production. If it's
  automated software calling an interface, the persona is that software — not a human.
- If you run a command to **emulate** what a real consumer does, **say so explicitly** in
  the How section: "We emulate what the consumer does in production by sending the same
  request it would send."
- The test must reflect **production reality, not testing convenience.** A story describing
  something no real user would ever do is a unit test pretending to be an acceptance test.

## The Banner — Mandatory on Every Evidence File

Every evidence file starts with a plain-language banner. It is not optional — it is what
makes the evidence readable by anyone. Five parts:

| Part | What it says | Bad | Good |
|------|-------------|-----|------|
| **Who** | Which persona is doing this, and why them | "Reviewer (CLI)" | "The security reviewer — their job is to verify that error messages don't leak internal details an attacker could use." |
| **What** | What they're doing, what changed, business context — plain English | "Send a request with a bad input" | "A consumer sends data to the system to check whether it should be trusted. The data is invalid. Before this fix the system said exactly why; now it gives a generic message." |
| **Why** | What goes wrong for real users if this fails | "Rule R3 must hold" | "If the system reveals why the check failed, an attacker learns how to craft a better forgery — knowing the format was right and only one detail was wrong." |
| **How to run** | Steps a QA person can follow; if emulating a consumer, say so | "run the command with a bad value" | "Source the environment file. We emulate what a consumer does in production by sending the same request, deliberately with bad input, to see what comes back." |
| **Expected** | The passing result — plain language first, then the technical detail | "Generic error" | "The system says the input is invalid but does NOT reveal why — nothing about the internal reason." |

**Banner language rules — write like you're explaining to a manager, not an engineer:**

- GOOD: "The operator signs in to the system using the command-line tool. Before this
  change, sign-in needed two fields; now it needs one."
- BAD: "The operator authenticates using the new auth shape with constant-time comparison
  and receives a short-lived token."
- GOOD: "If this old address still answers, someone with a stolen key could get in."
- BAD: "If the endpoint is still registered in the router, the bootstrap flow remains
  exploitable via replay."

**The banner tells a story, not a checklist:** a character (who), a situation (what changed
and what they're doing), stakes (why — what breaks if this fails), and a resolution (what a
good outcome looks like). **If the Why doesn't make a non-technical person uncomfortable
about the failure scenario, it's too abstract.**

## Evidence Discipline

- **One story at a time.** Run one, capture the output, write the verdict, then the next.
  Firing stories in parallel loses the output.
- **Banner goes IN the call.** The who/what/why/how/expected is written into the evidence
  file by the same command that runs the test — not as a separate step.
- **Output pipes into the file.** Command output goes straight into the evidence file with
  `>> "$F" 2>&1`. No copy-paste later.
- **Display the file after** with `cat "$F"` so the reader sees the complete evidence.
- **The verdict is EARNED.** Never write PASS before you see the output. Read the result,
  then write the verdict.

The single-call shape (use the project overlay's real personas, commands, and paths):

```bash
F=tests/<phase-or-fix>/evidence/story-<ID>-<slug>.md
cat > "$F" << 'BANNER'
# <ID> — <Story Title>

Who: <persona from the project overlay, and why them>.

What: <what they're doing, what changed, business context — plain English>.

Why: <what breaks for real users if this fails>.

How to run: <steps a QA person can follow; if emulating a consumer, say so>.

Expected: <plain-language pass first, then the technical detail>.

## Test Output

BANNER
<the real command from the project overlay> >> "$F" 2>&1
echo "" >> "$F"; echo "## Verdict" >> "$F"; echo "" >> "$F"
cat "$F"
```

Then, after reading the output, append the earned verdict:

```bash
echo "PASS — <plain-language statement of what the output proved>." >> "$F"
```

## Evidence Structure

Every phase or fix gets its own directory under `tests/`:

```
tests/<phase-or-fix>/
  user-stories.md     — all stories with personas, steps, acceptance criteria
                        (Infrastructure Prerequisites table first)
  <env file>          — environment variables, sourced once before testing
  evidence/
    README.md         — summary table with verdicts + open issues
    story-<ID>-<slug>.md — one file per story: banner + output + verdict
```

- **One file per story**, named `story-<ID>-<slug>.md`. If a story runs in more than one
  deployment mode, each mode is a separate section in the same file.
- **Source the env file once** — don't inline environment variables on every command.
- The `evidence/README.md` is a summary table (story, description, persona, tool, verdict)
  plus an Open Issues section.

## Rules

1. **Read the project overlay first.** Personas, modes, and commands there override every generic example here.
2. **Real artifacts only.** The thing that ships, run for real. Never an interpreted shortcut, never a unit test.
3. **Stories first.** Write the user stories (and the prerequisites table) before running anything.
4. **Personas don't mix.** Each persona uses its own tool, as the overlay defines.
5. **Banner is mandatory.** Every evidence file opens with who/what/why/how/expected in plain language.
6. **At least one real third-party acceptance story.** The system talking to itself is not acceptance.
7. **Unverifiable prerequisite = STOP.** No acceptance testing against missing infrastructure.
8. **One story at a time.** Run, record output, write verdict, next.
9. **Output goes in the file.** Piped directly, not copy-pasted.
10. **Verdict is earned.** Never PASS before seeing the output.
