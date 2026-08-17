---
name: openspec-onboard
description: >-
  Guided, narrated walkthrough of one complete OpenSpec cycle — explore,
  create a change, build proposal/specs/design/tasks, implement, archive —
  using a real small task in the user's own codebase. Use for "walk me
  through OpenSpec", "teach me the OpenSpec workflow", "onboard me to
  OpenSpec", or a first-time user asking what OpenSpec even is. Does real
  work, not a simulation; teaches by doing rather than lecturing.
compatibility: "Requires the openspec CLI on PATH; stops early with a clear message if it is not installed. The external grilling skill is optional — used only if a genuine design uncertainty comes up."
---

# Onboard to OpenSpec

Teach the full OpenSpec cycle by actually running it, once, on a small real
task — narrating each step as you go rather than lecturing first and doing
later.

## Preflight

Check the CLI is installed (`openspec --version`, or the PowerShell
equivalent). If not installed, say so and stop: "OpenSpec CLI is not
installed. Install it first, then come back to onboarding."

## Phase 1: Welcome

State what will happen (pick a small real task, explore it briefly, create a
change, build proposal -> specs -> design -> tasks, implement, archive) and
that it takes about 15-20 minutes, then move to task selection.

## Phase 2: Task selection

Scan the codebase for small opportunities: `TODO`/`FIXME`/`HACK`/`XXX`
comments, swallowed errors or risky operations without try/catch, functions
missing tests, `any`/`as any` in TypeScript, stray `console.log`/`debugger`,
user input handlers missing validation. Check recent activity with
`git log --oneline -10`. Present 3-4 specific, concretely-scoped suggestions
(location, scope estimate, why it's good) plus a "something else?" option; let
the user pick or describe their own. If nothing turns up, ask directly what
small thing they've been meaning to fix.

**Scope guardrail:** if the chosen task is a major feature or multi-day work,
say so and offer to slice it smaller, pick something else, or do it anyway —
a soft guardrail; respect the user's choice if they insist on the larger
scope.

## Phase 3: Explore demo

Briefly demonstrate explore mode (`/openspec-explore`) on the chosen task: 1-2
minutes reading the relevant file(s), an ASCII diagram if it helps, a short
note of considerations. Explain that this is what `/openspec-explore` is for —
thinking before implementing, usable any time. **Pause** for acknowledgment
before creating the change.

## Phase 4: Create the change

Explain a "change" is a container for the work, living at
`openspec/changes/<name>/`, holding proposal/specs/design/tasks. Run
`openspec new change "<derived-name>"` and show the resulting folder
structure.

## Phase 5: Proposal

Explain the proposal captures why and what, at a high level. Draft it (Why,
What Changes, Capabilities — new/modified, Impact) and show it before saving;
**pause** for approval or feedback. After approval, get instructions
(`openspec instructions proposal --change "<name>" --json`) and save to
`openspec/changes/<name>/proposal.md`. Note it can always be refined later.

## Phase 6: Specs

Explain specs define what, precisely, in testable WHEN/THEN terms. Create
`openspec/changes/<name>/specs/<capability-name>/` and draft one
`### Requirement:` with a `#### Scenario:` using WHEN/THEN(/AND), noting
scenarios read like test cases. Save to
`openspec/changes/<name>/specs/<capability>/spec.md`.

## Phase 7: Design

Explain design captures how — decisions and tradeoffs — and that brief is
fine for small changes. Draft Context, Goals/Non-Goals, and at least one
named Decision with rationale. Save to `openspec/changes/<name>/design.md`.

**Optional: resolve open questions with grilling.** If the design draft left
a real trade-off unresolved — several plausible approaches, an ambiguous
Decision, an open question you can't confidently answer for the user — offer
to run the external `/grilling` skill on it before moving to tasks: "This
design has an open question about `<X>`. Want to run a quick grilling pass to
pin it down before we break it into tasks?" This is a soft offer, not a gate
— if the user declines, or `/grilling` is not installed, say so (naming
`/setup-rhdh-skills install` as the next step) and move on with the design as
drafted. If the user accepts, invoke `/grilling`, fold the resolved answers
back into `design.md`'s Decisions, then continue to Phase 8. Skip this
entirely when the design has no real open question — most small onboarding
tasks won't.

## Phase 8: Tasks

Explain tasks break the work into checkboxes that drive implementation. Draft
numbered groups of `- [ ] N.M <task>` checkboxes plus a verification group.
**Pause** for confirmation the user is ready to implement, then save to
`openspec/changes/<name>/tasks.md`.

## Phase 9: Apply (implementation)

Explain implementation happens task by task, checked off as you go. For each
task: announce it, implement it in the codebase, reference the spec/design
naturally ("the spec says X, so I'm doing Y"), flip `- [ ]` to `- [x]`, brief
status line. Keep narration light — teach without over-explaining every line.
After all tasks, confirm completion and move to archiving.

## Phase 10: Archive

Explain archiving moves the change to
`openspec/changes/archive/YYYY-MM-DD-<name>/` and becomes part of the
project's decision history. Run `openspec archive "<name>"` and show the
archive location.

## Phase 11: Recap and next steps

Recap the full cycle (Explore -> New -> Proposal -> Specs -> Design -> Tasks
-> Apply -> Archive) and show the command reference:

| Command | What it does |
|---|---|
| `/openspec-propose` | Create a change and generate all artifacts |
| `/openspec-explore` | Think through problems before/during work |
| `/openspec-apply-change` | Implement tasks from a change |
| `/openspec-archive-change` | Archive a completed change |
| `/openspec-new-change` | Start a new change, step through artifacts one at a time |
| `/openspec-continue-change` | Continue working on an existing change |
| `/openspec-ff-change` | Fast-forward: create all artifacts at once |
| `/openspec-audit-change` | Audit artifacts for cross-artifact consistency (pre-implementation) |
| `/openspec-verify-change` | Verify implementation matches artifacts (post-implementation) |
| `/grilling` (external, optional) | Resolve a genuinely open design question before moving to tasks |

Close with: "Try `/openspec-propose` on something you actually want to build."

## Graceful exit handling

**User wants to stop mid-way:** reassure the work is saved at
`openspec/changes/<name>/`, and that `/openspec-continue-change` or
`/openspec-apply-change` picks it back up later. Exit without pressure.

**User just wants the command reference:** show the table above (same
content) and stop the tutorial there.

## Guardrails

- Follow EXPLAIN -> DO -> SHOW -> PAUSE at key transitions (after explore,
  after the proposal draft, after tasks, after archive).
- Keep narration light during implementation — teach without lecturing.
- Don't skip phases even for a tiny change — the goal is teaching the
  rhythm, not speed.
- Pause for acknowledgment at marked points; don't over-pause elsewhere.
- Handle exits gracefully — never pressure the user to continue.
- Use a real codebase task — never simulate or fabricate one.
- Guide toward a smaller scope gently; respect the user's final choice.

## Completion

Complete when the user has either finished a full real cycle through archive,
or exited gracefully at a marked pause point with their change intact and
saved — and, either way, can name which `/openspec-*` skill to reach for next.
