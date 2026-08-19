---
name: openspec-new-change
description: >-
  Scaffolds a new OpenSpec change directory and shows the first artifact's
  template without writing it. Use for "start a new change", "scaffold a
  change", "set up a change for X", or "what's the first artifact I need" —
  when the user wants the folder and the first template, not a drafted
  proposal. Creates the change directory and stops — it does not draft or
  write any artifact content, and it does not implement code. To generate
  every artifact in one pass, use openspec-ff-change instead.
compatibility: "Requires the openspec CLI on PATH."
---

# Start a new OpenSpec change

Scaffold a change directory and hand the caller the first artifact's template,
then stop — drafting content is a separate turn.

## Steps

1. **Get the change name.** If the user's request already names it or
   describes what they want to build, derive a kebab-case name (e.g. "add
   user authentication" -> `add-user-auth`). Otherwise ask what they want to
   build or fix; do not proceed without knowing.
2. **Pick the schema.** Run `openspec schemas --json` to see what this
   project actually offers. `rhdh-spec-driven` is the default only when the
   repo's `openspec/` has it configured — its `config.yaml` and
   `schemas/rhdh-spec-driven/` files live in `/rhdh-spec-driven-schema` and
   must be present under the repo's `openspec/` for `--schema rhdh-spec-driven`
   to resolve. If it is not listed, name an available schema instead. Omit
   `--schema` to take the configured default.
3. **Create the change directory:**

   ```bash
   openspec new change "<name>" [--schema <name>]
   ```

   This scaffolds `openspec/changes/<name>/` with the selected schema.
4. **Show status:**

   ```bash
   openspec status --change "<name>"
   ```
5. **Get the first artifact's instructions.** Find the first artifact with
   `status: "ready"` in the status output (schema-dependent — `proposal` for
   `rhdh-spec-driven`), then:

   ```bash
   openspec instructions <first-artifact-id> --change "<name>" --json
   ```

   Read `/rhdh-spec-driven-schema` for what the `context`, `rules`, and
   `template` fields in that response mean before summarizing them.
6. **Stop and wait for user direction.**

## Output

Summarize: change name and location, schema and its artifact sequence,
current status (0/N artifacts complete), and the template for the first
artifact. Close with: "Ready to create the first artifact? Describe what this
change is about and I'll draft it, or ask me to continue."

## Guardrails

- Do not create any artifacts yet — only show the first artifact's template.
- Do not advance beyond the first artifact.
- If the name is not valid kebab-case, ask for a valid one.
- If a change with that name already exists, suggest `/openspec-continue-change`
  instead of creating a duplicate.

## Completion

Complete when the change directory exists, its schema is confirmed, and the
first artifact's template has been shown to the user — without any artifact
file having been written yet.
