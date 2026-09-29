---
name: review
description: Use when asked to review, check or counter-review a Squelette-governed project or a new Squelette version, or to write a review mandate for another AI — read-only posture, reproducible failure scenarios, closed verdicts, lettered report.
license: MIT
compatibility: Written for Squelette 3.21.1. Reading the project's files and running its read-only commands needs git and Python 3.
---

# Independent review of a Squelette-governed project

Written for Squelette **3.21.1**. The repository under review is the authority on its own rules (`AGENTS.md`, `docs/agent-governance/AGENTS.core.md`, `project_control/README.md`); this skill describes how a reviewer behaves, not what the project must contain.

A reviewer never writes as a contributor. If you are asked to both build and review the same object: `STOP` and ask the Project Owner to choose.

---

## 1. Posture

`READ_ONLY` / `REPORT_ONLY`, without exception. Absolutely forbidden: any write outside the single deliverable; `fetch`, `pull`, `push`, `checkout`, `commit`, `tag`, `branch` on the object under review; producing a candidate fix, an ADR, a Project Control record or a human decision; overwriting an existing deliverable.

A review runs in a copy registered by the original (the keys table, since 3.21.0), never in the original itself, and comes back with its mandate. Throwaway experiments happen in a separate folder of disposable repositories (`runs/`), never in the copy of the object.

When your deliverable recommends an action in another folder than the one in the mandate, present it as a decision for the human — original or copy, exact scope — never as a step already agreed.

## 2. Independence

- One independent session per mandate.
- A reviewer does not read another reviewer's deliverable before handing in their own.
- A counter-review reads the object **and** the deliverable it counter-reviews, never the parallel counter-review. It replays each finding in a fresh copy — or establishes it by reading the code, saying so —, classifies it confirmed or rejected, and looks for what the report missed. No fix before the Project Owner decides.
- A reconciliation reads everything and is not handed in by a first-round reviewer.

## 3. Preflight: the exact object

Before writing anything, check and report the object exactly as the mandate states it: path, commit, tree, number of tracked files, `skeleton_version` and number of core files (`core-manifest`), number of tests (run the suite). If one value differs: verdict `…_PREFLIGHT_STATE_MISMATCH` and stop — you do not review an object other than the one in the mandate. Read every document the mandate lists, in full, before writing.

## 4. Redline standard

**No demonstrated failure scenario, no redline.** An observation without a scenario stays an observation and blocks nothing. A finding carries: identifier, severity, object, reproducible failure scenario, evidence (path, lines, bytes, SHA-256), impact, proposed correction, status. Each scenario declares its **privileges** — commit gate installed or not, controller commands or direct writes — and the severity follows from them.

Fictitious records or decisions are allowed only as fixtures in the disposable folder, marked "REVIEW FIXTURE — FICTITIOUS, AUTHORIZES NOTHING". Never fabricate authority in the object itself.

## 5. Closed verdicts

A terminal verdict belongs to a closed list, prefixed by the object reviewed:

- `…_PASS`
- `…_REQUIRES_MINOR_REDLINE`
- `…_REQUIRES_MAJOR_REDLINE`
- `…_CANONICAL_CONFLICT`
- `…_INPUT_MISSING`
- `…_PREFLIGHT_STATE_MISMATCH`
- `…_OUTPUT_ALREADY_EXISTS`

Counter-review: `…_REVIEW_OF_<X>_PASS`, `…_REQUIRES_MINOR_RECONCILIATION`, `…_REQUIRES_MAJOR_RECONCILIATION`, plus the last three above. A verdict outside the list is a defect of the deliverable, not a nuance. An ambiguous or contradictory instruction is written in the report and stops that point: you do not decide in the Project Owner's place; `UNKNOWN` stays `UNKNOWN`.

## 6. Report structure

Lettered sections, in this order: preflight (exact state of the object: path, HEAD, hashes); inventory; exhaustive diff where relevant; replay of every applicable scenario; findings; redlines; blockers; authority conflicts; decision matrix; final Git state; terminal verdict. When the project's reporting style is `PLAIN`, the report starts with a three-point summary for the Project Owner; the body stays technical.

Single deliverable, at the path the mandate declares, append-only: a correction creates a linked revision (`V2`, erratum), never a silent rewrite.

## 7. Writing a review mandate for another AI

- **One granted folder**, created by the Project Owner and registered as a copy: `source/` (the object at its tag, read-only, read-only commands only), `runs/` (disposable experiments), the single deliverable at the root. The original and every real project stay out of reach.
- State the exact object to verify first (commit, tree, number of files, version, number of tests) and the `…_PREFLIGHT_STATE_MISMATCH` stop.
- Posture `READ_ONLY` / `REPORT_ONLY`; never attribute to the Project Owner a decision they did not make.
- Themes to examine, each with the question to answer; closed verdicts prefixed by the object; a three-point summary at the top of the report.
- **Word it as a test**: "robustness review", "standard / degraded configuration", "reproducible case". Some providers stop sessions whose wording reads like an attack, even for a legitimate review of your own project.
- Hand over the mandate in full, to paste into a fresh session. It comes back into the repository with the report.
