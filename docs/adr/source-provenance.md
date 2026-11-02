# ADR source provenance

Imported: 2026-11-03. Input: owner-provided Shared Agent Platform ADRs v1.1, dated 2026-11-02.

## Import boundary

The 15 numbered ADRs retain their IDs, dates, status headers, decision text, Related relationships, and explicit amendments. Repository changes to those records are relative-link relocation and formatting only. Do not silently rewrite accepted decisions; record a substantive change with an explicit amendment or a superseding ADR.

Supporting contracts, validation gates, references, original owner questions, historical changelog/check report, template, and contract registry are retained. Later owner selections are recorded separately in [project decisions](project-decisions.md). The [Cloudflare guide](cloudflare-reference-guide.md) adds verified reading order and compatibility evidence without selecting deployment versions.

## Verification

All 27 original payload hashes matched [the input manifest](source-sha256sums.txt). All 14 original static audit groups passed on a temporary mirror containing exactly those manifest-listed payloads and the manifest. The extracted download had an extra Finder .DS_Store; the original directory was left untouched and that operating-system metadata was excluded from the distribution mirror.

Those checks establish source consistency, not runtime qualification. Repository link, vocabulary, ADR-status, and semantic-preservation checks are separate from the original distribution audit. The copied manifest identifies input bytes; it is not a checksum manifest for reformatted repository files.

## Historical archive

The original v1.0 ZIP has SHA-256 3fd1d8ba0159cf6e242b9b9f390083365c853b92e6c8e356f8fecbc774919158. It may contain private historical references and is not committed to this public repository. The original owner-provided delivery remains the source of that archive. The active v1.1 documents mark private evidence as not publicly accessible; no public retrieval link or archived v0.3 copy is asserted.

## Input-to-repository mapping

Hashes below identify source files before editorial link and formatting changes.

| Source file                                       | Repository copy                                                                                | Source SHA-256                                                   |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| adr/001-platform-scope-and-reuse.md               | [001-platform-scope-and-reuse.md](001-platform-scope-and-reuse.md)                             | ce351b9d6ee0149708247ca24da5e976f7f88016b6d92880469fa26db1603113 |
| adr/002-existing-harness-and-template-baseline.md | [002-existing-harness-and-template-baseline.md](002-existing-harness-and-template-baseline.md) | da755ea1de4fbe7458725f3322e1a27b808db4281c8babe317f4874ec2dc042e |
| adr/003-model-providers-and-cost-control.md       | [003-model-providers-and-cost-control.md](003-model-providers-and-cost-control.md)             | b393bccb7c5cbd1b99188ed5d131460ddc897eeb1354822b2cff91ce881425c8 |
| adr/004-executor-contracts-and-portability.md     | [004-executor-contracts-and-portability.md](004-executor-contracts-and-portability.md)         | 5a735a8ae6a4e463b2bd86ae971c13ac4e89129a37872ae997c981c99167a086 |
| adr/005-state-ownership-and-durable-execution.md  | [005-state-ownership-and-durable-execution.md](005-state-ownership-and-durable-execution.md)   | 9992902f556f52f71b323052e1d691873fe3f40e3bdf164a8657856105ae2d45 |
| adr/006-authorization-and-external-effects.md     | [006-authorization-and-external-effects.md](006-authorization-and-external-effects.md)         | 77e503f8b5ef4e604b88d9d5b4e81f7e123ae4020403a2f4db4ced3cb64aef62 |
| adr/007-tools-skills-and-configuration.md         | [007-tools-skills-and-configuration.md](007-tools-skills-and-configuration.md)                 | d0b5cbbcb52d62e11137ec8c1bb9c13a877ccef4ac251991051c1a3eecbf74bd |
| adr/008-cloud-edge-and-hardware-extension.md      | [008-cloud-edge-and-hardware-extension.md](008-cloud-edge-and-hardware-extension.md)           | cd50c9f0a9d75bc2c5570709096abbac7adc8af0e96bcd46b7a400a6d67de28a |
| adr/009-observability-evaluation-and-recovery.md  | [009-observability-evaluation-and-recovery.md](009-observability-evaluation-and-recovery.md)   | 2a5cf24e6d417a2ff8cb7bc915154481ba8fb660c5b0d952bd9d53aa88b57a8e |
| adr/010-mvp-scope-and-release-gates.md            | [010-mvp-scope-and-release-gates.md](010-mvp-scope-and-release-gates.md)                       | e95a6e09fc58e63627e35e25e72702a68a5b4eecc9a580adf461fe4998fd19ee |
| adr/011-telegram-and-human-approval.md            | [011-telegram-and-human-approval.md](011-telegram-and-human-approval.md)                       | 892d4b4fd38708fc4c5a3a6737564e46788728ec7151eeceedd5b808ee6510c6 |
| adr/012-shared-memory-and-context-service.md      | [012-shared-memory-and-context-service.md](012-shared-memory-and-context-service.md)           | 42b2a0f2684efaea62ef39ec89caee6b4fbcb4addd8c49b3f46ae145fdce0d94 |
| adr/013-event-driven-superagent-coordination.md   | [013-event-driven-superagent-coordination.md](013-event-driven-superagent-coordination.md)     | fc2c9dc4052ca0fda8de230bd41e78b78471c09fcae4c41b76a82e22cba0a9ea |
| adr/014-identity-and-authorization.md             | [014-identity-and-authorization.md](014-identity-and-authorization.md)                         | a05d2b2cd84f934f2a8166ea2a447104e5aeae4036109e04d8b9a4e93bfc617f |
| adr/015-operational-baseline.md                   | [015-operational-baseline.md](015-operational-baseline.md)                                     | 85499127ada9b56ea571dd095b8a2dc774e84499a52526cae404afe98a2076ce |
| adr/TEMPLATE.md                                   | [template.md](template.md)                                                                     | 534d87d883761f948ad5f88fa92643c77517472103d922d9777e3abe1b8759c1 |
| README.md                                         | [README.md](README.md)                                                                         | 29dd3a7528be51eda4c3b84c5df2c9fd99660fbfc51cebf9b2bfafaf3abe9095 |
| CONTRACTS.md                                      | [contracts.md](contracts.md)                                                                   | 84616baa9ca10ffeebba79e13c7e7aa9b387493831252d3b527fa2e8ff97ac67 |
| VALIDATION.md                                     | [validation.md](validation.md)                                                                 | 3e6ca30f3ce4fcae226eca95cea30145cd125bf28735a18e1d2be797b2c55232 |
| REFERENCES.md                                     | [references.md](references.md)                                                                 | b8f45a812ffff29e6dca6732851a72a349b33fe8b481cc4b0e25bec29e1a6d04 |
| OPEN-DECISIONS.md                                 | [open-decisions.md](open-decisions.md)                                                         | 9509f06d04deabbb9b706f490586021ff875e583b3a2d508662f7a7f2064047f |
| CHANGELOG.md                                      | [baseline-changelog.md](baseline-changelog.md)                                                 | f300c2b9d996fceaae475972ffdfd38853c947f6f5fd4257db8ab2bc9b47e798 |
| CHECK-REPORT.md                                   | [baseline-check-report.md](baseline-check-report.md)                                           | d36bcb768b9e0e356680e8fd2d0839bfe08e8004914f394d664e6753f0341659 |
| checks/contract-registry.json                     | [contract-registry.json](contract-registry.json)                                               | c69e291183e55a50e69ab9374576e559e9421de3ea7dadc3b236fcf719190839 |
