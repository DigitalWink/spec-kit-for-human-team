# Spec Kit

A toolkit for Spec-Driven Development: templates, command files, and the `specify` CLI that installs them for a chosen coding agent.

## Language

### The constitution and its consumers

**Constitution**:
The project's standing policy document at `.specify/memory/constitution.md`. Durable technical and process policy, not a values charter.
_Avoid_: charter, tenets, principles doc

**Consumer**:
A command template that loads the constitution at runtime. Ten reference it; four carry enforcement teeth.
_Avoid_: reader, client

**Gate**:
A point where a consumer can block on a constitution violation. `/plan` fills `## Constitution Check` and errors; `/analyze` rates a MUST conflict as CRITICAL.

**Tier**:
Which consumer can observe a violation of a given rule: **plan-visible** (answerable from `plan.md` or `tasks.md`), **converge-visible** (answerable from feature-scoped source), or **human-gate**. Decides admission; never written into the constitution itself.

### The constitution interview

**Interview**:
The bounded, inference-first flow that produces a constitution. Brownfield-primary: it infers from the repo, then asks the user to confirm or correct.

**Area**:
One topic the interview probes. **Always-ask** areas run on every project; **conditional** areas run only when a repo signal fires their trigger.

**Trigger**:
The repo signal that fires a conditional area. A product-surface fact, never the presence of a tool that would already enforce the rule.

**Admission test**:
The three tests that decide whether a candidate rule becomes a principle — **gate-restatement** (it restates as a plan checkbox), **forbidden-alternative** (it names the cheaper thing an engineer would otherwise do), and **evidence** (it names the artifact that answers the checkbox). Defined in [issue #5](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5).
_Avoid_: quality check, filter

**Candidate rule**:
A statement offered for the constitution, before the admission test judges it.

**Route-out**:
Refusing a candidate rule and naming where it belongs instead — a linter config, `AGENTS.md`, an ADR, the spec, or nothing.
_Avoid_: reject, drop, exclude

**Routing table**:
The report the interview prints after writing the constitution: one line per routed-out candidate, saying what it was and where it went. Lives outside the constitution file.

**Surface contract**:
The area covering the product's own external surface — command verbs, flag names, endpoint names, output shapes, exit codes, their stability, and the documentation that ships with a change.

**Amendment interview**:
The short "what changed?" flow that runs when a constitution already exists, instead of authoring one from scratch.

### Packaging

**Preset**:
A bundled layer that overrides or wraps core command templates without modifying them. `presets/lean` and `presets/constitution-sync` are the working precedents.

**Integration**:
A supported coding agent, as a self-contained subpackage under `src/specify_cli/integrations/`.
_Avoid_: agent adapter, provider

**Extension**:
An opt-in add-on that owns behavior the CLI deliberately does not, such as `agent-context` owning the managed section of `CLAUDE.md` and `AGENTS.md`.
