<!--
SYNC IMPACT REPORT
==================
Version change: (none) → 1.0.0
Bump rationale: Initial authoring run. 4 principles admitted, 5 obligations.
Principles added: I, II, III, IV
Principles modified: (none)
Principles removed: (none)
Sections added: (none)
Sections removed: (none)
Principles carried forward unchanged: (none)
-->

# tally Constitution

## Core Principles

### I. Negative scope

This stays a single-user local tool. A plan MUST NOT introduce a user account, a login, a session, or a server component.

### II. Technology and dependency boundaries

A plan MUST NOT add a runtime dependency without recording, in Complexity Tracking, the standard-library API it rejected.

### III. Surface contract

A plan MUST NOT introduce a command verb outside `init`, `run` and `status`, and MUST NOT change the shape of `--json` output without naming the replacement.

### IV. Testing and verification

A plan MUST state, in Technical Context, the automated test for each behavior change it introduces.

## Governance

Amendments to this constitution are approved by Me, until there is someone else.

**Version**: 1.0.0 | **Ratified**: 2026-08-26 | **Last Amended**: 2026-08-26
