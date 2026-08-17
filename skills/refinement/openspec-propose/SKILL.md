---
name: openspec-propose
description: >-
  Proposes a new OpenSpec change and generates every artifact it needs for
  implementation in one step, framed as the friendly "describe what you want,
  get a complete proposal back" entry point. Use for "propose a change for
  X", "I want to build X, write it up", or "give me a proposal with design and
  tasks ready to implement" — especially as someone's first change. Mechanics
  are identical to openspec-ff-change; this skill exists for the softer
  framing new users reach for. If a change already exists, use
  openspec-continue-change or openspec-ff-change on it instead.
compatibility: "Requires the openspec CLI on PATH."
---

# Propose an OpenSpec change

Turn a description of what someone wants to build into a complete, ready-to-
implement change: proposal (what and why), specs (what, precisely), design
(how), and tasks (the checklist) — all in one step.

## Steps

1. **Get the change name or description.** Derive a kebab-case name if only a
   description was given; do not proceed without knowing what to build.
2. **Create the change directory:** `openspec new change "<name>"`. This
   scaffolds `openspec/changes/<name>/` including `.openspec.yaml`.
3. **Get the build order:**

   ```bash
   openspec status --change "<name>" --json
   ```

   Read `applyRequires` and the `artifacts` dependency list.
4. **Create every required artifact in dependency order**, tracking progress
   with TodoWrite. For each ready artifact, follow the artifact creation loop
   in `/rhdh-spec-driven-schema` (`references/artifact-loop.md`) — read
   dependencies, use the template, apply context/rules without copying them
   into the file, write to `outputPath`, verify it exists. Re-check status
   after each write; continue until every `applyRequires` ID is `done`.
5. **If something is critically unclear,** ask — otherwise make a reasonable
   decision and keep moving; the point of this skill is momentum toward a
   complete proposal.
6. **Show final status:** `openspec status --change "<name>"`.

## Output

Change name and location, each artifact created with a brief description,
"All artifacts created! Ready for implementation.", and: "Run
`/openspec-apply-change` to start working on the tasks."

## Guardrails

- Create every artifact `applyRequires` needs, not a partial set.
- Always read dependency artifacts before creating the next one.
- Verify each artifact file exists after writing before proceeding.
- If a change with that name already exists, ask whether to continue it
  (`/openspec-continue-change`) rather than silently overwriting it.

## Completion

Complete when every artifact ID in `applyRequires` shows `status: "done"`,
each was verified to exist on disk, and the user has been pointed at
`/openspec-apply-change` for implementation.
