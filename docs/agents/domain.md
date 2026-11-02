# Domain docs

## Layout

This is a single-context repository:

- CONTEXT.md at the repository root contains the domain glossary.
- docs/adr/ contains architecture decision records.

## Before exploring

Read CONTEXT.md when present, then ADRs relevant to the work.
If those documents are absent, proceed silently; create domain records lazily when terms or decisions are resolved through the domain-modeling workflow.

Use the [domain glossary](../../CONTEXT.md), [ADR index](../adr/README.md), [project choices](../adr/project-decisions.md), and [Cloudflare reference guide](../adr/cloudflare-reference-guide.md) for the current vocabulary and architecture evidence. The [platform specification](../../plans/20261103-0000-shared-agent-platform/shared-agent-platform-spec.md) defines the staged feature scope.

## Vocabulary and decisions

Use glossary terms in issues, hypotheses, tests, and refactor proposals.
If a needed term is missing, identify the gap rather than inventing a conflicting synonym.
Surface any conflict with an existing ADR explicitly; do not silently replace its decision.
Keep accepted decisions distinct from recommendations and unresolved configuration.
