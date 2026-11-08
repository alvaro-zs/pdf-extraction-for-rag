# Issue tracker: GitHub

Issues and specs live in [alvaro-zs/pdf-extraction-for-rag](https://github.com/alvaro-zs/pdf-extraction-for-rag/issues). Use the `gh` CLI from this repository.

## Conventions

- Before the first issue creation in a session, verify the target with `gh repo view --json nameWithOwner`. The expected repository is `alvaro-zs/pdf-extraction-for-rag`.
- Create: `gh issue create --title "..." --body "..."`. For multiline content, write the exact text to a temporary file and use `--body-file <path>`.
- Read: `gh issue view <number> --json number,title,body,labels,comments`.
- List: `gh issue list --state open --json number,title,body,labels,comments`. Add appropriate state and label filters.
- Comment: `gh issue comment <number> --body-file <path>`.
- Apply or remove labels: `gh issue edit <number> --add-label "..."` or `--remove-label "..."`. Use [the triage label mapping](triage-labels.md) for triage roles.
- Close: post the explanation with `gh issue comment`, then run `gh issue close <number>`.

## Parent issues and dependencies

Use native GitHub sub-issues and blocking dependencies when available. For API operations, obtain the issue's numeric database ID with `gh api repos/alvaro-zs/pdf-extraction-for-rag/issues/<number> --jq .id`.

- Add a child: `gh api --method POST repos/alvaro-zs/pdf-extraction-for-rag/issues/<parent>/sub_issues -F sub_issue_id=<child-database-id>`.
- Add a blocker: `gh api --method POST repos/alvaro-zs/pdf-extraction-for-rag/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-database-id>`.
- Count open blockers: `gh api repos/alvaro-zs/pdf-extraction-for-rag/issues/<number> --jq .issue_dependencies_summary.blocked_by`.

Where native relationships are unavailable, put `Part of #<parent>` at the top of each child and maintain a task list in the parent. Record dependencies with `Blocked by: #<number>, #<number>` at the top of the child. A ticket is startable when all blockers are closed.

## Pull requests as a triage surface

**PRs as a request surface: no.**

## Skill operations

When a skill says to publish to the issue tracker, create a GitHub issue. When it says to fetch a ticket, read the issue with its labels and comments.

## Wayfinding operations

- Map: one issue labelled `wayfinder:map`, containing Notes, Decisions-so-far, and Fog.
- Child tickets: link them to the map as sub-issues, or use the parent task list fallback above. Apply the corresponding `wayfinder:<type>` label: `research`, `prototype`, `grilling`, or `task`.
- Frontier: inspect the map's open children in map order; choose the first unassigned ticket with no open blockers.
- Claim: `gh issue edit <number> --add-assignee @me` before working on the ticket.
- Resolve: comment with the answer, close the ticket, then append a short finding and link to the map's Decisions-so-far.

Create a required wayfinding label if it is missing when a wayfinding workflow first uses it.
