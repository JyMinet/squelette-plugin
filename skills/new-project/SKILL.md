---
name: new-project
description: Use when the user wants to start a new project governed by Squelette — export the latest public release, create a standalone Git baseline, install the commit gate, then hand over to FIRST_START.md.
license: MIT
compatibility: Needs git (2.26 or later), tar and Python 3, and network access to github.com to fetch the public template (github.com/JyMinet/squelette).
---

# Start a new Squelette-governed project

This skill only sets the stage. From step 6 on, the project's own `FIRST_START.md` is the authority, not this skill.

Squelette governs projects built with AI agents: work happens only in Work Items authorized by a recorded human decision, a small controller in the repository refuses what is outside the authorized scope, and nothing is `DONE` without evidence. Say this to the Project Owner in one or two sentences if they have not used it before.

## 1. Agree on the folder — double stop

Ask the Project Owner where the new project should live. It must be a folder that does not exist yet, or is empty. Never reuse an existing repository, and never work inside the Squelette template itself.

Creating that folder is leaving the current folder, so apply the double stop:

- `STOP 1`: name the exact folder, what will be written there (an export of the template and a new Git repository), and the alternative (nothing written: you only print the commands); wait for the answer.
- `STOP 2`: restate the exact scope — this folder, a new local Git repository on `main`, no remote, no push — and wait for a second, distinct confirmation that names the folder. "ok" or "go" alone do not count.

## 2. Find the latest release (read-only)

```bash
git ls-remote --tags --refs https://github.com/JyMinet/squelette.git 'v*' \
  | sed 's#.*refs/tags/##' | sort -V | tail -1
```

Show the tag to the Project Owner. Use a promoted release tag, never a branch.

## 3. Export the tracked tree

Use `git archive`, not a copy of the visible files: the template has a tracked hidden file, `.gitignore`, that a file manager leaves behind.

```bash
TAG="<tag from step 2>"
DEST="<confirmed project folder>"
SRC="$(mktemp -d)"
git -c advice.detachedHead=false clone --quiet --depth 1 --branch "$TAG" https://github.com/JyMinet/squelette.git "$SRC/squelette"
TEMPLATE_COMMIT="$(git -C "$SRC/squelette" rev-parse HEAD)"
mkdir -p "$DEST"
git -C "$SRC/squelette" archive HEAD | tar -x -C "$DEST"
git -C "$SRC/squelette" ls-files -z > "$SRC/files.nul"
```

## 4. Create the standalone Git baseline

No inherited history, no remote. Stage the exported files explicitly — never `git add .` or `git add -A`:

```bash
cd "$DEST"
git init -b main
git add --pathspec-from-file="$SRC/files.nul" --pathspec-file-nul
git commit -m "init: pristine copy of Squelette $TAG"
rm -rf -- "$SRC"   # the temporary clone only, created in step 3
```

## 5. Install the commit gate and check

```bash
python3 -B scripts/project_control.py install-gate
python3 -B scripts/project_control.py bootstrap-audit     # must be PASS
python3 -B scripts/project_control.py status              # NOT_STARTED, Bootstrap Mode
```

If `bootstrap-audit` does not pass, stop and report what it says; do not fix the template.

## 6. Hand over to FIRST_START.md

Read `FIRST_START.md`, `AGENTS.md` and `docs/agent-governance/AGENTS.core.md` in full, then conduct the initialization exactly as `FIRST_START.md` describes it: the Project Owner's interview (facts, assumptions and unknowns kept apart; nothing inferred that they did not decide), including the two mandatory questions — **reporting style** (`TECHNICAL` or `PLAIN`) and **language** (`FR` or `EN`) — then `bootstrap-preflight` before each group of writes, and `bootstrap-closeout`. Record the Project Owner's ideas in their own words as they come (`idea add`, no commit in Bootstrap Mode).

When filling `provenance/TEMPLATE_PROVENANCE.md`, record the real ancestry: the public repository, the tag and `TEMPLATE_COMMIT` from step 3, the method (`git archive` of the tracked tree), inherited history `NONE`, inherited remote `NONE`.

From then on, the `guide` skill applies.
