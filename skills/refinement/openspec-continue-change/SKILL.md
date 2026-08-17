---
name: openspec-continue-change
description: >-
  Continues an OpenSpec change by creating exactly the next ready artifact,
  one at a time. Use for "continue this change", "what's next", "create the
  next artifact", or when the user wants to progress a change step by step
  rather than generating everything at once. Stops after one artifact; use
  openspec-ff-change or openspec-propose instead when the user wants every
  remaining artifact created in one pass.
compatibility: "Requires the openspec CLI on PATH."
---

# Continue an OpenSpec change

Create one artifact — the next one whose dependencies are satisfied — and
stop, so the user reviews each step before the next is generated.

## Steps

1. **Select the change.** If not named or inferable from context, run
   `openspec list --json` and ask the user to pick from the most recently
   modified 3-4 changes, marking the most recent as "(Recommended)". Never
   guess.
2. **Check status:**

   ```bash
   openspec status --change "<name>" --json
   ```

   Read `schemaName`, `artifacts[].status`, and `isComplete`.
3. **Act on status:**
   - **All complete (`isComplete: true`)** — congratulate, show final status,
     suggest implementing (`/openspec-apply-change`) or archiving
     (`/openspec-archive-change`). Stop.
   - **An artifact is `ready`** — pick the first ready one and create it by
     following the artifact creation loop in `/rhdh-spec-driven-schema`
     (`references/artifact-loop.md`). Create exactly one artifact, then stop.
   - **Nothing is ready and nothing is complete** — this should not happen
     with a valid schema; show status and suggest checking for a schema
     issue.
4. **Show progress:** `openspec status --change "<name>"`.

## Output

After each invocation: which artifact was created, the schema in use, current
progress (N/M complete), and what is now unlocked. Close with: "Want to
continue? Just ask me to continue or tell me what to do next."

## Guardrails

- Create exactly one artifact per invocation — never more.
- Never skip artifacts or create them out of order.
- If context for the artifact is unclear, ask before creating it.

## Completion

Complete when exactly one artifact was written (verified to exist on disk)
and the user has seen updated progress and what it unlocked — or, if already
complete, when that state was reported without attempting a write.
