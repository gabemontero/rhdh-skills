---
name: rhdh-pr-review
description: >-
  Reviews the code in a Red Hat Developer Hub pull request: fetch its diff,
  linked issues and CI status, analyze the changes, draft inline comments, post
  the review to GitHub, and for an rhdh-operator PR optionally deploy its
  CI-built bundle onto a live OpenShift cluster and verify the change there. Use
  for a GitHub PR URL or number, "review this PR", analysis-only review, inline
  comments, posting a review, testing operator PR images or bundles on a
  cluster, or a combined code and cluster review. For label and merge-readiness
  triage of the overlay PR backlog, use /rhdh-overlay.
compatibility: "GitHub CLI and Python 3; oc plus an accessible cluster for operator testing. Requires the external /code-review skill — analysis is blocked without it."
---

# RHDH Pull Request Review

Keep forge I/O at the edges: fetch produces the PR context, analysis works from
that context and checked-out code alone, and posting sends only findings already
verified against the head SHA. Cluster testing reads the same context
independently.

## Route by outcome

| Outcome | Workflow sequence |
|---|---|
| Code review and post | `workflows/fetch-github.md` → `workflows/review-code.md` → `workflows/post-to-github.md` |
| Analysis only | `workflows/fetch-github.md` → `workflows/review-code.md`; stop after the edited draft |
| Test an rhdh-operator PR | `workflows/fetch-github.md` → `workflows/review-operator-pr.md` |
| Full review | fetch → review code → confirm and post → operator cluster test |

A bare PR URL or number defaults to code review and post. For an
`rhdh-operator` PR, offer full review because code and deployable bundle changes
can diverge, but respect an explicit route.

Load `workflows/fetch-github.md` to gather PR context, including CI confirmation
and the Spec source. Load `workflows/review-code.md` to run `/code-review`,
verify, and draft. Load `workflows/post-to-github.md` to post a GitHub review.
Load `workflows/review-operator-pr.md` to test an operator bundle on a cluster.

## Review invariants

- `/code-review` runs on every path that drafts a review, including
  analysis-only. If it is missing, stop, say that `code-review` is missing, and
  name `/setup-rhdh-skills install`. Present its Standards and Spec reports as
  their own reports, then draft the GitHub review from verified findings.
- Specialists named in the original request are the specialist set. The default
  team is `/code-review`'s two agents. A PR is very small when `files[]` length
  is ≤ 3, `totalAdditions + totalDeletions` is under 80, and the user did not
  ask for a team. Fan out further only when it is not very small, or the user
  asked. Load `references/review-perspectives.md` when fanning out.
- One inline per merge-shaped problem or lasting rule. Cluster nits into one
  comment or a single top-level "also" paragraph.
- Verify every finding against code at the fetched head SHA. Drop stale,
  duplicated, speculative, or convention-conflicting findings.
- Present the complete edited draft and review event for confirmation before
  stating any post operation. An explicit request to post is intent, not approval
  of the exact write.
- For cluster testing, deploy the full PR bundle or manifests, not only the
  operator binary image. Preserve and report the original cluster state and
  cleanup result.

## Write gate

Fetch and analysis are read-only. Posting a GitHub review, posting a test-request
comment, or changing cluster resources is an external write: invoke the named
skill `mutation-gate` and follow the gate it owns rather than restating it
here. Creating or removing a local git worktree is not that gate.

A review operation's target pins the head SHA; a cluster operation's target names
the namespace. An earlier confirmation of findings approves no write. Report each
outcome with the changed resources or review URL, the verification done, the
cleanup state, and any recovery still owed.

## What each stage carries forward

Every stage passes its result to the next in conversation. The field names are
defined once, where they are produced:

| Stage | Result | Defined in |
|---|---|---|
| Fetch | PR context: repository, changeRequest, files, diff, linkedIssues, jiraKeys, existingComments, existingReviews, ciStatus, specSource | `workflows/fetch-github.md` |
| Analysis | Review draft: changeRequest, summary, verdict, findings, edited, worktreePath | `workflows/review-code.md` |
| Operator testing | Subject, per-check results, verdict, cluster state, cleanup | `workflows/review-operator-pr.md` |

## Scripts and references

- `scripts/fetch_pr_context.py` deterministically builds the PR context as one
  JSON object with no envelope.
- `references/review-perspectives.md` routes extra lenses once the default team
  is not enough.
- `references/operator-pr-images.md` defines operator bundle/image extraction.

## Completion

Complete when the report names the head SHA reviewed, has presented the
`/code-review` Standards and Spec reports, presents the edited draft, gives the
outcome of every approved write with its target, includes the cluster check
results when operator testing ran, and states every skipped check or cleanup
action with its reason.
