Integrate the Claude workflow into the current repository “$ARGUMENTS”.

1. **Create missing directories**: docs, docs/impl, docs/plan, docs/ADRs, .claude/commands.
2. **Write missing canonical docs** with stub content as listed below. **Do not overwrite existing files.**
3. **Write repo‑local helper command files** into `.claude/commands/` (prompts provided after the list).
4. **BASH** `git add` all new files.
5. Remind user: `git commit -m "chore: add Claude workflow"`.

### Stub files & content

If `CLAUDE.md` **does not exist**
← insert full template between `<<<CLAUDE>>>` markers below.
If `CLAUDE.md` **already exists**
→ **do NOT overwrite.**
instead: output the contents between `<<<CLAUDE>>>` markers below and ask the user to add it manually to their `CLAUDE.md`.

README.md – `# $REPO_NAME`
PRD.md – `# PRD for $REPO_NAME`
docs/architecture.md – `# Architecture\n_TODO: add system diagram_`
docs/glossary.md – `# Glossary\n| Term | Definition |`
docs/api-guide.md – `# Public API Guide\n<!-- AUTO START -->\n<!-- AUTO END -->`
docs/ADRs/README.md – `# ADRs\nThis folder follows MADR 1.2 format.`
docs/onboarding.md – `# Onboarding Checklist`
CHANGELOG.md – empty

### Repo‑local helper command files & prompts

Write each file only if absent.

`regen-docs.md`

> Evaluate the Documentation Policy in CLAUDE.md; update any stale docs; `git add` changes.

`list-docs.md`

> Read policy table; print ✅/❌ for each doc; no file writes.

`doc.md`

> Regenerate the canonical doc named `$ARGUMENTS` right now and `git add` it.

`update-changelog.md`

> Prepend "$ARGUMENTS – $(date +%Y-%m-%d)" to CHANGELOG.md; `git add CHANGELOG.md`.

`docs/typedoc-merge.md`

> Run `typedoc --json api.json`; merge into docs/api-guide.md between AUTO markers; delete api.json; `git add docs/api-guide.md`.

`next.md`

> Scan issues/\*.md; output the first unchecked box whose predecessors are done.

`analyze-complexity.md`

> Load docs/impl/<id>.md; score bullets 1‑10; append Complexity table; add subtasks to issues/<id>.md; `git add` modified files.

`review-self.md`

> Spawn a new Claude sub-task to review the diff on HEAD.
> Goal: spot over-engineering, unused variables, typecasts, and oversized commits.
> Output a concise list of critique bullets. (No file writes.)

`think-hard.md`

> Before answering, “think ultrahard” and explore at least three alternative solutions.
> Return the simplest viable one plus a short rationale.

<<<CLAUDE>>>

# 🚦 Global Claude Guide

u/PRD.md
u/README.md
u/docs/architecture.md
u/docs/glossary.md

<!-- Feature docs injected at runtime -->

---

## Principles

1. **Plan → Tests → Code → Docs → Commit/Push** for every feature.
2. Work on branch `feature/<id>-<slug>`.
3. Doc‑keeper keeps canonical docs fresh.

## Workflow Table (Claude MUST follow)

| Stage          | Claude’s tasks                                                                                                                                                                                                                                            | Git action                            |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| **Branch**     | Create & switch branch.                                                                                                                                                                                                                                   | `git checkout -b feature/<id>-<slug>` |
| **Plan**       | `/plan take docs/prd_<id>.md and create plan` → writes plan + issues.                                                                                                                                                                                     | commit `plan`/`issues`                |
| **Guide**      | Write `docs/impl/<id>.md`.                                                                                                                                                                                                                                | commit guide                          |
| **Tests**      | Write failing tests.                                                                                                                                                                                                                                      | commit tests                          |
| **Code**       | Implement until tests pass.                                                                                                                                                                                                                               | commit feat/fix                       |
| **Verify**     | Run package scripts `typecheck`, `test`, and `lint` (e.g. `pnpm run typecheck`, `bun run test`, `npm run lint`) until **all three exit 0**. If any script is missing, ask the user which command to run instead. Fix issues and re-run before continuing. | commit `chore: fix lint/ts`           |
| **Doc-keeper** | Auto-refresh stale docs.                                                                                                                                                                                                                                  | commit docs                           |
| **Changelog**  | `/project:update-changelog`.                                                                                                                                                                                                                              | commit changelog                      |
| **Push & PR**  | Push branch & open PR.                                                                                                                                                                                                                                    | `git push -u origin && gh pr create`  |

## Documentation Policy

**Only triggers & mandatory status belong in this table. Content details live in the next section.**

| Doc                             | Trigger                                | Mandatory |
| ------------------------------- | -------------------------------------- | --------- |
| docs/architecture.md            | New service/DB/ext‑dep                 | ✅        |
| docs/api-guide.md               | Public API change                      | ✅        |
| docs/glossary.md                | New domain term                        | ✅        |
| docs/ADRs/                      | Decision → Accepted                    | ✅        |
| CHANGELOG.md                    | Feature merged                         | ✅        |
| README.md (root)                | Root setup/CLI change                  | 🔁        |
| (packages \| apps)/\*/README.md | Package‑level scripts, Docker changes  | 🔁        |
| (packages \| apps)/\*/CLAUDE.md | Tech‑stack or env changes for that pkg | 🔁        |

### Content guidelines

| Doc                                 | Recommended content style                                                                       |
| ----------------------------------- | ----------------------------------------------------------------------------------------------- |
| **docs/architecture.md**            | • One **C4 Context/Container diagram** in Mermaid.<br>• Runtime-flow bullets.<br>• ≤ 12 nodes.  |
| **docs/api-guide.md**               | • TypeDoc AUTO section.<br>• Hand‑written usage examples above AUTO block.                      |
| **docs/glossary.md**                | • `Term \| Definition` table.<br>• Single‑line defs; ADR/code links.                            |
| **docs/ADRs/**                      | • MADR 1.2 template.<br>• `NNNN-title.md`; status flow `Proposed` → `Accepted`.                 |
| **CHANGELOG.md**                    | • `## [x.y.z] – YYYY‑MM‑DD` header per release.<br>• Keep Unreleased header on top.             |
| **README.md (root)**                | • Shield badges.<br>• Quick‑start (`pnpm install && pnpm dev`).<br>• Link to CLAUDE.md.         |
| (**packages \| apps)/\*/README.md** | • Purpose + public API.<br>• Run/test/deploy instructions.<br>• Docker snippet if relevant.     |
| (**packages \| apps)/\*/CLAUDE.md** | • Tech stack & env vars.<br>• Any workflow deviations.<br>• Claude guidelines for this package. |

---

### Doc‑keeper Reflex

After **green tests** or `/project:regen-docs`:

1. Evaluate policy.
2. If any ✅ doc stale → WRITE update & `git add`.
3. If any 🔁 doc stale → ask unless `/force-docs` flag.

## Safety Rails

- Stage early, stage often – run `git add -p` after any good turn to protect progress.
- Prefer static analysis; **Tests, type-checker, and linter must pass before any commit.**
- no destructive flags unless user approves or requested.

## Project Commands

/project:regen-docs  /project:list-docs  /project:doc  /project:update-changelog  /project:docs:typedoc-merge  /project:next  /project:analyze-complexity
<<<CLAUDE>>>
