# Code Review Perspectives

Thin router for extra review focus after the default `/code-review` team.
Starting points, not a fixed roster. Load this when the PR is not very small, or
the user named extras. Spec coverage lives in `/code-review`; do not run a third
Requirements pass.

Specialist domain knowledge lives in whatever skill the user already named. Do
not invent a default specialist list.

## Common perspectives

| Perspective | Focus | Prompt guidance |
|-------------|-------|-----------------|
| **Correctness** | Logic bugs, edge cases, error handling, off-by-ones, null/undefined paths | "Find bugs that would reach production. Ignore style." |
| **Security** | Injection vectors, auth/authz gaps, secrets exposure, input validation | "Flag vulnerabilities with severity ratings." |
| **Adversarial** | Abuse of a new script, hook, parser, or path/auth handling | "Break the new surface. Assume hostile input." |
| **Architecture** | Module boundaries, coupling, abstraction levels, extensibility | "Evaluate structural impact. Is this change in the right place?" |
| **Performance** | Hot paths, query patterns, algorithmic complexity, caching | "Flag measurable performance risks." |
| **Compatibility** | Public API surface, breaking changes, deprecations | "Determine if changed symbols are public-facing before flagging." |

## Signals that suggest a perspective

Use these as hints, not rules. A PR may need perspectives not listed here, or may not need ones that signal-match.

| Signal | Suggests | Example |
|--------|----------|---------|
| Changes span 2+ modules/packages | Architecture | `src/api/` + `src/worker/` |
| New files created | Architecture | New module, new component |
| Diff adds a script, hook, parser, or path/auth handling | Adversarial | New CLI flag parser, webhook signature check, skill path join |
| README-only changes | skip Adversarial | docs-only PR |
| Changed paths match DB/query patterns | Performance | `**/model*`, `**/migration*`, `**/schema*` |
| Keywords in title/body | Performance | `optimization`, `latency`, `cache`, `slow` |
| Changed paths match API surface | Compatibility | `**/api/**`, `**/proto/**`, `**/openapi*` |
| Package version changes | Compatibility | `package.json`, `pyproject.toml` version bumps |
| Labels | Varies | `refactor` → Architecture, `breaking` → Compatibility |

## Choosing perspectives

Read the PR's diff, metadata, and `specSource`. Create perspectives based on what matters most for this specific change — the examples above are a starting point, not a menu to pick from.

## Reviewer coordination

When using extra reviewers beyond the default `/code-review` team:

- The parent creates the worktree when needed and passes its path
- Each extra reviewer gets the diff, `files[]`, `specSource`, and their focus
- They read source at HEAD and verify; they do not draft GitHub review prose
- Reviewers should challenge overlapping or contradictory findings
