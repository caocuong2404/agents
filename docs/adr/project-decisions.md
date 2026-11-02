# Project decisions and remaining configuration

Recorded: 2026-11-03. Evidence: the owner selections and draft review summarized in [specification issue #1](https://github.com/caocuong2404/agents/issues/1).

This register applies later owner choices to the [v1.1 architecture baseline](README.md). It does not amend accepted architecture, claim a runtime gate passed, or turn a recommendation into authorization.

## Confirmed choices

| Area                       | Confirmed choice                                                                                                                  | Boundary still to qualify                                                          |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| First useful outcome       | Carry accepted project decisions and sources across clients and fresh sessions.                                                   | Exact public fixtures and measured outcome evidence.                               |
| Client scope               | Devin CLI/Desktop/cloud sessions, Codex, ChatGPT Plus, Meta AI website/mobile, and a third-party Grok bot on Telegram or Discord. | Exact surfaces, versions/plans, authentication, and actual connection paths.       |
| Workspace topology — OD-02 | One owner Workspace with project resource scopes.                                                                                 | Actual Principal inventory, per-client actions/selectors, and executor enrollment. |
| Initial data — OD-11       | Public or invented technical fixtures.                                                                                            | Dataset, destinations, and any later personal-project data authorization.          |
| Intended reliance          | Retained daily memory.                                                                                                            | Retention, deletion, backup, recovery, and restore evidence before reliance.       |
| Primary test seam          | Authenticated MCP/HTTP application-call boundary, confirmed during setup draft review.                                            | Real transport/client conformance and runtime tests.                               |

All named clients remain in target scope. A two-client Gate 1 demonstration proves the common contract; it is not evidence that every client is integrated. The Grok bot is operated by a third party; the owner cannot change its code/configuration. Meta AI consumer access and that bot's connection path remain unverified.

## Open choices

The original [owner-decision register](open-decisions.md) remains a historical baseline. OD-02 and OD-11 are only partially resolved by the choices above. Identity/bootstrap/recovery mappings, credentials, policy values, exact grants, and applicable execution settings still require configuration.

No retention duration, backup boundary/destination, RPO/RTO, automatic conversation extraction, or manual context-transfer fallback was selected. Unanswered popovers are not decisions.

The managed Agent Memory backend remains optional and in private beta. Published package versions in the [Cloudflare guide](cloudflare-reference-guide.md) are qualification candidates, not deployment pins. Model/workload selection and paid use remain subject to the existing gates.

## Maintaining decisions

Populate explicitly open configuration with its owner evidence and applicable gate. If a change alters an accepted architecture decision, add a numbered ADR or an explicit amendment and reciprocal links. Preserve prior status, rationale, and source history.
