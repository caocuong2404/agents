# Issue tracker: GitHub

Specs and tickets live in GitHub Issues for caocuong2404/agents.
Use the gh CLI from this clone; explicitly target --repo caocuong2404/agents when needed.

## Operations

- Create: gh issue create --repo caocuong2404/agents --title "TITLE" --body-file BODY_FILE
- Read: gh issue view NUMBER --repo caocuong2404/agents --comments
- List: gh issue list --repo caocuong2404/agents --state open --json number,title,body,labels,comments
- Edit: gh issue edit NUMBER --repo caocuong2404/agents --body-file BODY_FILE
- Comment: gh issue comment NUMBER --repo caocuong2404/agents --body-file BODY_FILE
- Labels: gh issue edit NUMBER --repo caocuong2404/agents --add-label LABEL or --remove-label LABEL
- Close: gh issue close NUMBER --repo caocuong2404/agents

Replace uppercase placeholders with the intended values. Write exact multiline content to a temporary file and pass --body-file. Apply the labels requested by the invoked skill using the triage mapping.

When a skill says "publish to the issue tracker", create a GitHub issue.
When a skill says "fetch the relevant ticket", read the issue with its comments and labels.

## Pull requests as a triage surface

PRs as a request surface: no.

GitHub shares issue and PR numbers; resolve the object type before using type-specific commands.
