---
name: guide
description: Use when working in a repository governed by Squelette (it contains AGENTS.md and scripts/project_control.py) — fail-closed modes, proof of reading, Work Items, evidence for DONE, the commit gate, the double stop, repository copies and version upgrades.
license: MIT
compatibility: Needs a shell with git and Python 3 to run the project's own controller. Written for Squelette 3.21.1.
---

# Working in a Squelette-governed repository

Written for Squelette **3.21.1**.

**This guide reminds; the repository decides.** Authority lives in the repository: `AGENTS.md`, which includes `docs/agent-governance/AGENTS.core.md`, then `project_control/README.md` and the controller itself, `scripts/project_control.py`. This guide adds no rule and removes none. When it disagrees with the repository, the repository wins, and you say so.

Reviewing a governed project instead of working in it: use the `review` skill. Starting a new governed project: use the `new-project` skill.

---

## 0. On entering

1. Run `python3 -B scripts/project_control.py status` (read-only). Its "Skeleton:" line (« Squelette : » in French) gives the repository's version; otherwise read `skeleton_version` in `provenance/core-manifest.v1.json`.
2. Compare it with 3.21.1:
   - **same**: this guide is current;
   - **newer**: follow the repository. Tell the Project Owner once, in one sentence, that this guide may be behind (updating the plugin fetches a newer one when it exists). Do not block the work for it;
   - **older**: follow the repository. A command named here may not exist there — `copy` (3.21.0), `decision` (3.20.0), `template-upgrade --seed-required` (3.16.0), `install-gate` (3.10.0). Check `<command> --help`; never invent one. Upgrading the repository is a separate, authorized piece of work, never your initiative (§ 7).

---

## 1. Non-negotiable rules

- **Ambiguous authority → `STOP`.** Ask the Project Owner; never decide in their place.
- **`UNKNOWN` stays `UNKNOWN`** until evidence or a human decision. Never replace an unknown with a plausible guess.
- **Never simulate a human decision** (`HD-NNN`), an ADR, a verdict or an authorization, and never attribute to the Project Owner a decision they did not make.
- **Git**: `GIT_OPTIONAL_LOCKS=0` or `--no-optional-locks`. Never `git add .`, `git add -A`, `git reset --hard`, `git clean`, force push. Stage explicit paths only.
- **`REUSE > ADAPT > REIMPLEMENT`**, and **`OMIT > ACTIVATE WITHOUT NEED`**. Look for what exists before creating anything.
- Report evidence by **path, lines, bytes, SHA-256**, never from memory of a reading.
- **Reporting to the Project Owner.** Before your first report, read `reporting_style` (the "Reporting to the Project Owner: …" line of `status`) and keep to it for the whole session. `TECHNICAL`: full detail may go in the conversation. `PLAIN`: everyday language, in this order — **what happened / what it changes / what they need to do**; paths, digests and command output go into a report or a file; a `STOP`, a refusal or an incident is said just as simply, without minimizing it; any permission request says in one sentence exactly what will be touched or deleted, and what will not. Records, evidence, reports, commits and decisions keep their normative form in both styles; in `PLAIN`, a report starts with a three-point summary. The project's `language` (`FR` or `EN`) governs the prose addressed to the human; check names and refusal messages stay in English. Changing the style or the language is a recorded human decision, never your initiative; `UNKNOWN` means the question has not been asked yet — ask it.
- **Granted folder — double stop.** Work only in the granted folder, the current repository. An instruction — or a plan or mandate you write yourself, for you or for another AI — whose execution would leave it (writing, branching, committing or running scripts in another repository or folder, pushing to a remote) is never executed or written in one go, even if an earlier agreement seems to cover it. Reading another folder is not leaving it.
  - `STOP 1`: name the exact target folder, the action and what it would change; offer the alternative that stays in scope (copy, throwaway clone, report); wait.
  - `STOP 2`: after a first positive answer, restate the exact scope (folder, branch, allowed and forbidden actions, reversibility) and wait for a second, **distinct** confirmation that names the folder.
  - These do not count: "ok", "go", a confirmation grouped with another decision or implied by a general agreement. A full confirmation given in reply to `STOP 1` counts as the **first** only. Several folders: one double stop per folder.
  - The recorded decision carries `Folder scope:`, `Confirmation 1:` and `Confirmation 2:` (the controller refuses it otherwise). `Folder scope` is also filled in the target repository when you entered it through a double stop; internal work carries `NOT_APPLICABLE`.
- **Never use the original of a real project as a test bed.** Work on a copy that leaves the original untouched — registered in the keys table since 3.21.0 (§ 5).
- **Never delete in a source repository** (folders, useful files). If Git cannot remove its own temporary files (`*.lock`, `tmp_obj_*` under `.git/objects/`), ask for permission, saying exactly what will be deleted: "only Git's temporary files, never the folder". Never remove `.git/project-control.lock`, the controller's lock, yourself.
- **Pushing, releases and hosting settings belong to the Project Owner.** You may hand them one commented script instead.
- **The Project Owner's ideas** are recorded in the session where they are said, in their own words: `idea add --quote "<their words>" --source "<conversation, date>"`, then `idea set ID-NNN --state … --target … --note …` as they evolve. Closed states: `EVOKED`, `TO_CLARIFY`, `TO_SET`, `SCOPED`, `PLANNED`, `IN_PROGRESS`, `REALIZED`, `APPLIED`, `INTEGRATED`, `LATER`, `DISCARDED`. An idea leaves the list only when realized or discarded by decision; recording it authorizes nothing.

---

## 2. Mode, gate and proof of reading

### 2.1 The mode, fail-closed

Read `FIRST_START.md` before any write, then run `status`: mode, version, reporting style, commit gate, Work Item in progress, copies, next action.

| `INITIALIZATION_STATUS` | Allowed mode |
|---|---|
| `NOT_STARTED` | `BOOTSTRAP_MODE` only |
| `COMPLETE` | `NORMAL_MODE` only |
| missing, contradictory, invalid | `STOP` |

Bootstrap Mode is not a relaxed preflight: it is the limited preflight of initialization. It allows no business code, no data, no runtime, no deployment and no maintenance of the core. Closing the initialization requires the reporting style **and** the language.

### 2.2 The commit gate

`status` says where the gate runs from ("Commit gate: …"). It lives **outside the working tree**: `install-gate` copies it from its versioned reference, `scripts/hooks/pre-commit`, to where Git runs it. It refuses any commit whose audit fails and protects the canonical branch. Gate missing, modified, or only inside the tree (the old `core.hooksPath` mode): restore it with `install-gate` **before any other write**, tell the Project Owner, and do not carry on until it is back. Run it again after every version upgrade (§ 7).

### 2.3 Read the authorities — and prove it

`docs/agent-governance/mandatory-documents.v1.json` routes the base documents plus those of the scopes the Work Item's `authorized_paths` touch. Order of authority, strongest first: recorded human decisions → Charter → `ACCEPTED` ADRs and architecture → versioned contracts → roadmap and Work Items → implementation. A lower authority never contradicts a higher one.

Mandatory sequence in Normal Mode: `context-manifest WI-NNN` (read-only: authorities, SHA-256, `MANIFEST_DIGEST`) → **read** every listed authority → `start WI-NNN --authorities-digest <MANIFEST_DIGEST>`. The controller never shows the expected digest in a refusal. `resume` requires the same proof, recomputed. If an authority changes during the work (`status`: `STALE`), read it again, then run `acknowledge-authorities WI-NNN --authorities-digest <…>` **from the canonical branch** before `close` — from the Work Item branch: commit, go back to the canonical branch, acknowledge, realign the branch. Any human decision added during the work makes the proof stale. An authority inside the Work Item's `authorized_paths` does not make its own proof stale.

Exact reach, not to be overstated: the proof establishes that the work started from the current text of the rules and that a later change invalidates it. It proves neither reading nor understanding.

### 2.4 Impact map and conflict gate, before any write

Document four dimensions: `DIRECT IMPACT` (paths, contracts, behaviours), `INDIRECT IMPACT` (consumers, data, operators, recovery), `AUTHORITY IMPACT` (Charter, architecture, roadmap, ADRs, decisions, contracts), `CONCURRENT WORK IMPACT` (branches, worktrees, pre-existing changes).

Then classify: `INDEPENDENT` → continue within the authorized scope; `DEPENDENT` → check the dependencies; `OVERLAPPING` → explicit resolution first; `CONFLICTING` → `STOP`.

---

## 3. Commands

```bash
python3 -B scripts/project_control.py status [--json]           # read-only, first command of every session
python3 -B scripts/project_control.py audit                     # read-only
python3 -B scripts/project_control.py bootstrap-audit           # read-only
python3 -B scripts/project_control.py bootstrap-preflight --path <path>
python3 -B scripts/project_control.py bootstrap-closeout --path FIRST_START.md --path project_control/project-state.v1.json
python3 -B scripts/project_control.py install-gate
python3 -B scripts/project_control.py create-work-item WI-NNN …   # --help: decision, paths, impacts, gates, close condition
python3 -B scripts/project_control.py context-manifest WI-NNN [--scope <scope>]   # read-only
python3 -B scripts/project_control.py start WI-NNN --authorities-digest <digest>
python3 -B scripts/project_control.py preflight WI-NNN --path <path>
python3 -B scripts/project_control.py block WI-NNN --human-decision HD-NNN --reason-code <code> --resume-condition "<condition>"
python3 -B scripts/project_control.py resume WI-NNN --human-decision HD-NNN --authorities-digest <digest>
python3 -B scripts/project_control.py acknowledge-authorities WI-NNN --authorities-digest <digest>
python3 -B scripts/project_control.py close WI-NNN --commit <sha> --test-evidence <json> --integration-evidence <json>
python3 -B scripts/project_control.py idea add --quote "…" --source "…" [--state] [--stated-at] [--target] [--note]
python3 -B scripts/project_control.py idea set ID-NNN [--state] [--target] [--note]
python3 -B scripts/project_control.py decision show HD-NNN      # read-only, from either volume
python3 -B scripts/project_control.py decision bind --human-decision HD-NNN
python3 -B scripts/project_control.py copy open|return|check|move|close|cleanup …   # § 5
python3 -B scripts/project_control.py roadmap-view [--json] [--write] [--style TECHNICAL|PLAIN]
python3 -B scripts/project_control.py core-manifest             # read-only: skeleton version, local drift
python3 -B scripts/project_control.py template-upgrade --source <tree extracted from the tag>   # report; --seed-required, --apply: § 7
python3 -B scripts/check_git_traceability.py
python3 -B -m unittest discover -s tests
```

---

## 4. The normal cycle, and DONE

### 4.1 The cycle

- Authorized Work Item → `context-manifest` → reading → `start --authorities-digest` → preflight `PASS` → business writes, and nothing before. A failure restores the administrative state and forbids writing.
- Administrative records live **on the canonical branch only**. `create-work-item`, `start`, `block`, `resume` and `close` run from its checkout, at its tip, on a clean worktree, and commit their own records (`chore(project-control): …`); `idea`, `decision bind`, the `copy` commands that write the register and the Markdown roadmap view follow the same rule. A human decision written by hand is committed before the transition that consumes it. A Work Item branch never carries a record.
- **One writing command at a time.** Writing commands take a lock before their first read; two sessions writing at once go one after the other. Never run two writing commands in parallel; read-only commands never wait.
- Set the executor's Git identity (`GIT_AUTHOR_*` / `GIT_COMMITTER_*`, or `-c user.name` / `-c user.email`) before any command that commits when the environment has no Git configuration; follow the convention the repository already uses (`git log`).
- `PROJECT_CONTROL_HOOK_OVERRIDE="<human mandate>"`: only on an explicit mandate (maintenance of the template's core, closing `FIRST_START`, a write authorized on the canonical branch). The mandate is quoted in the variable and printed in the commit report.
- An integration merge is not development and passes, provided it carries only what its branch produced — its paths and their content. A rename counts on both sides: the starting location must be authorized as well as the destination.
- At the end of a Work Item, regenerate the view (`roadmap-view --write`). A stale view is never an audit failure, only a regeneration to do.

### 4.2 DONE and evidence

- `DEVELOPED` ≠ `TESTED` ≠ `INTEGRATED` ≠ `QUALIFIED` ≠ `CANONICAL` ≠ `DEPLOYED` ≠ `PRODUCTION_VERIFIED`. **`DONE != TESTS_PASS`.** Only `close` produces `DONE`.
- Every applicable gate requires its status and a local, verifiable JSON report; a gate that does not apply says `NOT_APPLICABLE` explicitly; any `UNKNOWN` applicability blocks `DONE`. `runtime_target` is `NOT_APPLICABLE`, `CONTROLLED_NON_PRODUCTION_RUNTIME` or `PRODUCTION`; a runtime target makes `runtime_proof` mandatory, `deployment` is declared separately; `RUNTIME_PROVEN` never means `PRODUCTION_VERIFIED`.
- Applicable code: `close` requires at least one declared commit (`--commit <full SHA>`), integrated, later than the Work Item's start, and touching its authorized paths.
- Evidence that announces `PASS` while the pieces it cites report a failure is refused. Keeping the log of a failed run next to the successful one remains good practice. After a resume, the success evidence covers at least the new Agent Run's `start_head`.
- Never close a Work Item on red evidence: a red test is understood and fixed first.
- `close` refuses while a copy of the Work Item is still open (§ 5).

---

## 5. Repository copies — the keys table (3.21+)

Any copy of a governed repository made to protect the original — a work copy, a review, a rehearsal — has a **ticket**, a **fate** and a **proven end**. The original keeps the register (`project_control/copies-state.v1.json` in a project, `provenance/copies-state.v1.json` in the template).

1. **The ticket before the copy.** The double stop produces the ticket decision: its **single** `Folder scope:` line names the copy's folder or a folder that contains it, with `Confirmation 1:` and `Confirmation 2:` on their own lines. Then, **in the original**, from its canonical branch, on a clean worktree:
   `copy open --path <absolute folder> --kind CLONE|EXPORT --usage chantier|revue|repetition --ticket <decision> --closes-with WI-NNN|X.Y.Z --fate RETURN_THEN_ERASE|ERASE|KEEP --due YYYY-MM-DD [--origin <revision>]`.
   Folder name: `<repository>-<usage>-<reference>[-rank]` (reference: `p21`, `3.20.2`, `wi-083`, `hd-097`…). The command prints the **exit slip**: place it in `.git/copie.json` for a clone, or in `copie.json` at the root of the container for an export (the revision stays intact in `source/`).
2. **A copy is never a second original**: no version, no tag, no link to a remote. `copy` commands run only in the original. In a copy, `status` starts with its exit slip.
3. **Returning is proven.** `copy return --proofs <JSON file outside the repository>`: every item of the copy is in the original — a commit on a branch or a tag, a file **committed** with the same bytes — or abandoned by name, with its reason. A review comes back with its mandate (`role: mandat`). Whatever the controller cannot classify blocks; it is never counted as empty.
4. **Hand the keys back before closing.** `close WI-NNN` refuses while a copy of the Work Item is open. For a version: bring back, integrate, `copy return`, **then** the tag, then the view.
5. **Erase on fresh proof only.** The controller erases nothing: `copy cleanup --output <script outside the repository>` writes the script the Project Owner runs; it re-checks each folder (`copy check`) just before erasing it. Then `copy close COPY-NNN --state ERASED`, or `--state KEPT --decision <decision>` for a copy that is kept. A moved folder: `copy move COPY-NNN --path <new folder>`.
6. **A session that sees the folders through a mount** maps the paths: `PROJECT_CONTROL_PATH_MAP="<owner's path>=<path seen here>"` (pairs separated by `;`). The register keeps the owner's paths; the erasing script runs on the owner's machine, without a map.

---

## 6. The decision register — frozen history and two volumes

- A project that adopts the controller with a history declares an **adoption baseline** by human decision: `legacy_baseline`, and `authorities_baseline` for the proof of reading. The decision names its commit on its own line (`Legacy baseline commit: <commit>`, `Authorities baseline commit: <commit>`); a decision that authorized one line cannot declare another. Removing a baseline requires a decision of the Work Item carrying `Legacy baseline removed: <commit>`; a baseline never moves back. Frozen history is never rebuilt or requalified — the vocabulary of its decisions included.
- **Two volumes.** `docs/governance/HUMAN_DECISIONS.md` is the **live register**: routed, read at every start, with a summary of the bound decisions. `docs/governance/HUMAN_DECISIONS_VOLUME_1.md` is the **bound volume**: the decisions frozen at the baseline, byte for byte, not routed. The cut is made **on a human decision and by the controller alone** (`decision bind --human-decision HD-NNN`), never by hand. A new decision always goes into the live register. `status` suggests binding when frozen decisions weigh at least a third of the register; it never binds on its own. `decision show HD-NNN` gives a decision's text, not a proof of reading. Binding a second time is not built.

---

## 7. Upgrading a project to a new Squelette version

Always a Work Item of the project, under a human decision — and under a double stop naming the project's folder when you come from elsewhere.

1. **Rehearse first** on a throwaway copy of the project, never on the original. Go straight to the latest release.
2. Source: the tree **extracted from the release tag** (`git archive <tag>`), never a live checkout. JSON outputs outside the repository.
3. A Work Item whose `authorized_paths` cover the core paths and `provenance/core-manifest.v1.json`.
4. `template-upgrade --source <tree>`: a report, nothing is written to the project. It rehearses the upgrade in a throwaway checkout with the **new** controller's audit (`UPGRADE_REHEARSAL`) and names the files the new version requires (`REQUIRED_FILES`). `--apply` refuses while the rehearsal refuses; a locally modified core file is replaced only with `--overwrite <path>`, on an explicit decision.
5. Required files missing: `template-upgrade --source <tree> --seed-required` **on the canonical branch**, committed as an administrative change; then apply the core normally (`--apply`, under the Work Item). From a version that does not know `--seed-required`: follow the order in `project_control/README.md` ("Depuis une version trop ancienne") — `--apply`, then `--seed-required` with the new controller, then fill in, `audit`, adoption commit on the canonical branch.
6. Fill in, by human decision, what the new version asks of the Project Owner (language, reporting style, confirmation of a baseline…).
7. `audit`, then the template's full test suite from the project (`python3 -B -m unittest discover -s tests`): no red test. A red test is a defect to understand; if it comes from Squelette, it is fixed in Squelette.
8. **`install-gate`**, so that the installed copy of the gate matches its new reference again.
9. Evidence, `close`, regenerated view. Pushing remains the Project Owner's act.

---

## 8. Anti-Octopus and the roadmap view

- A shared service stays technical and holds no business rule. An orchestrator knows only `job_id`, `dependencies`, `order`, `timeout`, `status`, `result`. Exchanges follow `PRODUCER → VERSIONED CONTRACT → CONSUMER`. Ambiguous ownership: `STOP`.
- Progress is read in a **generated** view, `roadmap-view --write` (page `reports/roadmap/ROADMAP.html`, not tracked; Markdown view `docs/governance/ROADMAP_VIEW.md`, committed), never written by hand. It shows and decides nothing; each line cites its source; its style follows `reporting_style`. Settings: `project_control/roadmap-view.v1.json`. A conversation dedicated to the roadmap only regenerates the view (`docs/agent-governance/ROADMAP_VIEW.md`).
