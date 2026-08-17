---
name: openspec-ff-change
description: >-
  Fast-forwards an existing or brand-new OpenSpec change through every
  artifact required for implementation, in one pass, with progress tracked
  via TodoWrite. Use for "fast-forward this change", "generate all the
  artifacts now", "skip the step-by-step and just build the proposal/specs/
  design/tasks", or "get this ready to implement". For the friendlier
  first-run framing of the same all-in-one generation, see openspec-propose;
  for one artifact at a time, see openspec-continue-change.
compatibility: "Requires the openspec CLI on PATH."
---

# Fast-forward an OpenSpec change

Generate every artifact the schema requires before implementation can start,
without pausing between them, then hand off to implementation.

## Steps

1. **Get the change name or description.** Derive a kebab-case name from a
   description if no name was given (e.g. "add user authentication" ->
   `add-user-auth`); do not proceed without knowing what to build.
2. **Create the change directory** (skip if it already exists — see
   Guardrails): `openspec new change "<name>"`.
3. **Get the build order:**

   ```bash
   openspec status --change "<name>" --json
   ```

   Read `applyRequires` (artifact IDs needed before implementation) and
   `artifacts` (status + dependencies for each).
4. **Create every required artifact in dependency order.** Track progress
   with TodoWrite. For each artifact whose dependencies are satisfied, follow
   the artifact creation loop in `/rhdh-spec-driven-schema`
   (`references/artifact-loop.md`) — read dependencies, use the template,
   apply context/rules without copying them into the file, write to
   `outputPath`. Re-check status after each write; stop once every ID in
   `applyRequires` reports `status: "done"`.
5. **If an artifact needs input the description didn't cover,** ask — but
   prefer a reasonable default to keep momentum; this skill exists to avoid
   stopping between artifacts.
6. **Show final status:** `openspec status --change "<name>"`.

## Output

Change name and location, every artifact created with a one-line description,
"All artifacts created! Ready for implementation.", and: "Run
`/openspec-apply-change` to start working on the tasks."

## Guardrails

- Create every artifact `applyRequires` needs — not a subset.
- Always read dependency artifacts before creating the next one.
- Verify each artifact file exists after writing, before moving on.
- If a change with that name already exists, suggest
  `/openspec-continue-change` on the existing change instead of overwriting it.

## Completion

Complete when every artifact ID in `applyRequires` shows `status: "done"` in
`openspec status`, each was verified to exist on disk, and the user has been
pointed at `/openspec-apply-change` to start implementation.
