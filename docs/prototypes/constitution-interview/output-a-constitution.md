<!--
SYNC IMPACT REPORT
==================
Version change: (none) → 1.0.0
Bump rationale: Initial authoring run. 8 principles admitted, 22 obligations.
Principles added: I, II, III, IV, V, VI, VII, VIII
Principles modified: (none)
Principles removed: (none)
Sections added: (none)
Sections removed: (none)
Principles carried forward unchanged: (none)
-->

# specify-cli Constitution

## Core Principles

### I. Negative scope

A plan MUST NOT add a code path in the Specify CLI that creates, updates, removes, resolves, or migrates an agent context or instruction file. A plan MUST NOT introduce a file write outside the project root, and MUST NOT make a consume or author command depend on network access.

### II. Technology and dependency boundaries

The runtime dependency set is: click, json5, packaging, pathspec, platformdirs, pyyaml, readchar, rich, typer. A plan MUST NOT add a runtime dependency outside that set. A plan that must add one MUST record it in Complexity Tracking, naming the standard-library API or the already-listed package it rejected. Runtime versions are declared as floors, never as pins.

### III. Architecture and structural limits

The distribution-artifact ceiling is 1. A plan MUST NOT raise the number of distribution artifacts declared in the build manifest above 1. A plan MUST NOT introduce an interface, abstract base class, or protocol with a single planned implementation, unless it is a declared extension point registered in the project's integration registry.

### IV. Surface contract

The measured command-verb grammar is: add, disable, enable, info, install, list, remove, search, set-priority, update. The measured recurring flag grammar is: --author, --commands-dir, --dev, --force, --from, --integration, --integration-options, --json, --model, --name, --offline, --output-format, --priority, --script, --skills, --tag. A plan MUST NOT introduce, in its Technical Context or its tasks, a command verb, flag name, endpoint name, or output shape outside those sets. A plan MUST NOT remove or rename a published verb, flag, endpoint, field, or exit code without naming the replacement. A plan that changes user-visible behavior MUST name the user-facing document it updates in the same change.

### V. Irreversible and destructive operations

A plan that adds a file removal, an in-place overwrite of user-supplied content, or an uninstall path MUST name, in Technical Context, the hash check that gates it and its no-clobber behaviour for user-modified files. A plan MUST NOT introduce a destructive action that runs without a preview of what it will change. A plan MUST NOT plan a file write or removal that resolves outside the project root, and a symlink that escapes the project root MUST be refused.

### VI. Testing and verification

A plan MUST state, in Technical Context, the automated evidence for each behavior change it introduces. A functional requirement whose only stated evidence is manual verification MUST NOT be planned. A plan that changes a command template under `templates/commands/` MUST name, in Technical Context, the agent it will be exercised through and the task that records the result.

### VII. Failure semantics and operating envelope

A plan that adds an operation which can fail at runtime MUST state, in Technical Context, what the user sees on failure and whether the operation retries under a bounded attempt count, degrades to a named fallback, or aborts with a non-zero exit status. An unbounded retry MUST NOT be planned.

### VIII. Supported platform and runtime matrix

The supported set is Linux, macOS and Windows on Python 3.11 or later. A plan MUST state its Target Platform and minimum runtime in Technical Context, and MUST NOT declare a runtime or platform outside that set without adding the matching CI matrix cell in the same change.

## Governance

Amendments to this constitution are approved by the project maintainers.

**Version**: 1.0.0 | **Ratified**: 2026-08-26 | **Last Amended**: 2026-08-26
