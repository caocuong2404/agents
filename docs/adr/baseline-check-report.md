> Repository note (2026-11-03): This is the historical static-check report shipped with v1.1, not a runtime result or a claim that this reorganized directory is the original distribution. See [source provenance](source-provenance.md) for current import checks.

# Static archive check report — v1.1

Date: 2026-11-02. **PASS: documentation/archive checks.** Nothing is deployed; no runtime, security, performance, provider-effect or hardware gate has been exercised. Accepted means implementation baseline.

| Requested check               | Result                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| sha256sum -c                  | PASS for all 27 payload files. SHA256SUMS.txt excludes itself by the documented self-hash convention; every other regular file is covered.                                                                                                                                                                                                                                                 |
| README status = ADR header    | PASS: 15/15 exact matches, including the seven original mismatches; all 13 original status values preserved. Qualification is separate.                                                                                                                                                                                                                                                    |
| Reciprocal Related links      | PASS: 68 reciprocal pairs; zero asymmetric or self pairs; no one-way exceptions. Required 006↔012, 006↔013, 004↔013, 012↔013 and 014/015 links present.                                                                                                                                                                                                                                    |
| S-ID integrity both ways      | PASS: S01–S26 defined and cited; zero undefined or unused IDs. New official vendor sources are S21–S26.                                                                                                                                                                                                                                                                                    |
| Contract vocabulary           | PASS: 12 entities, 29 calls, 14 closed enums, field/parameter definitions and gate tags checked. ADR code identifiers and prose concepts reviewed against CONTRACTS; legacy approval entity names removed.                                                                                                                                                                                 |
| Mutation fences / idempotency | PASS: all 24 declared mutating calls show a server-issued fence and idempotency_key. claim returns ExecutionFence; heartbeat/checkpoint/report/propose require it. Broker dispatch has its own service RecordFence plus a separate current execution_fence. Gate 3 specifies a stale case for each executor/operation mutation, including disabled-operation negative tests before Gate 4. |
| Runtime-validation wording    | PASS: active documentation reviewed; no affirmative claim that this platform has been runtime-validated or is production-ready. Future tests remain requirements.                                                                                                                                                                                                                          |

Additional checks passed: original ADR IDs/sections retained; all relative file links resolve; all ten v1.0 invariants preserved verbatim; substantive amendments have explicit headers/notes; every Task state is covered in 27 guarded transition rows; Gate 1 remains memory-only. The immutable v1.0 ZIP matches its recorded SHA-256.

The included audit script passed 14 static check groups. Seven deliberately broken scratch copies were independently rejected: status mismatch, one-way Related link, unknown S-ID, undefined field, missing mutation fence, affirmative runtime-validation sentence and payload tampering. These controls test the archive checker, not the agent platform. Controlled-identifier checking is lexical; prose terminology and decision semantics were also reviewed manually. This is not a formal proof of natural-language completeness or runtime behavior.

Official docs were checked for SQLite PITR, R2 locks/lifecycles, secret version deployment and rate-limit locality. Native R2 version-history recovery and coordinated DO/R2 restore were **not verified** and are explicitly not assumed. Recommendations do not select an owner recovery strategy.

All D1–D14 changes and EDITORIAL/SUBSTANTIVE classifications are mapped in CHANGELOG.md. OPEN-DECISIONS.md contains 14 owner items, each with 2–3 options and a recommendation. Retention/timing/limit values, actual identities/grants, recovery selection, the first Tier-1 fixture and overnight-expiry calibration remain OPEN. Existing numeric defaults are unchanged. Tier 2 stays disabled; no new product or always-on local runner is introduced.

Reproduce from the extracted agent-platform-adrs-v1.1 directory:

```sh
python3 checks/audit_archive.py
sha256sum -c SHA256SUMS.txt
```

The original private v0.3 source is marked not publicly accessible. The archived original is v1.0; no claim of a public or archived v0.3 copy is made.
