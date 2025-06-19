# 🌀 Claude-Code-Kickstarter

_The one-file setup that drops a full “plan → tests → code → docs → PR” workflow into **any** Git repo—powered by Claude Code._

---

## ✨ What is this?

Inside this repo you’ll find **one magic Markdown file**:

```
.claude/commands/setup-claude-workflow.md
```

Copy that file into _any_ existing project (monorepo, single package, JavaScript, Bun, TypeScript—doesn’t matter).
Then run:

```bash
/user:setup-claude-workflow "My Awesome Project"
```

Claude will:

1. **Detect** which canonical docs you already have.
2. **Write** the missing ones (architecture diagram stub, glossary, API guide, ADR README, onboarding checklist).
3. **Drop** a battle-tested `CLAUDE.md` that enforces:

   - _Branch → Plan → Tests → Code → Verify → Docs → Changelog → PR_

4. **Install** repo-local helper commands (`/project:regen-docs`, `/project:next`, etc.).
5. **Merge** a minimal GitHub Actions workflow if you don’t have one.
6. Stage everything with `git add` and politely remind you to commit.

No `git init`, no history rewrite, no yak-shaving.

---

## 🏄‍♂️ Quick start

```bash
# 1. add the command file
mkdir -p .claude/commands
curl -o .claude/commands/setup-claude-workflow.md \
  https://raw.githubusercontent.com/<your-fork>/claude-code-kickstarter/main/.claude/commands/setup-claude-workflow.md

# 2. run it
/user:setup-claude-workflow "My Awesome Project"

# 3. commit the goodies
git commit -m "chore: add Claude workflow"

# 4. write a PRD and let Claude take the wheel 🚗
echo "# PRD #42 – Super Search" > docs/prd_42.md
/plan take docs/prd_42.md and create plan
continue
```

---

## 📂 What gets added?

| Path                            | Purpose                                                                    |
| ------------------------------- | -------------------------------------------------------------------------- |
| **CLAUDE.md**                   | Guard-rails, workflow table, Doc-keeper policy, package-README guidelines. |
| **docs/architecture.md**        | C4/Mermaid diagram stub.                                                   |
| **docs/glossary.md**            | Empty _Term \| Definition_ table.                                          |
| **docs/api-guide.md**           | “AUTO START/END” markers for TypeDoc merge.                                |
| **docs/ADRs/README.md**         | MADR 1.2 template pointer.                                                 |
| **docs/onboarding.md**          | Checklist for new devs.                                                    |
| **docs/impl/** & **docs/plan/** | Empty folders so Git tracks them.                                          |
| **.claude/commands/**           | Repo-local helper prompts.                                                 |
| **.github/workflows/ci.yml**    | Vitest + type-check + lint + TypeDoc (merged if CI exists).                |
| **CHANGELOG.md**                | Empty changelog (kept fresh by `/project:update-changelog`).               |

Already got a `CLAUDE.md`?
_The setup command will **not** overwrite it._
Instead, it prints a diff and offers to run `/user:upgrade-claude-policy` which merges the new policy/table into your file.

---

## 🛡️ Recommended tool permissions

Add these to `~/.claude/settings.json` once and forget about permission prompts:

```json
{
  "permissions": {
    "allow": ["Bash(pnpm:*)", "Bash(bun:*)", "Bash(git:*)", "Bash(gh:*)"],
    "deny": []
  }
}
```

Feel free to swap `pnpm` for `npm` or `yarn`—Claude will ask if it can’t find a script.

---

## 🎛️ Daily driver tips

| Need                                   | Command                          |
| -------------------------------------- | -------------------------------- |
| Check which docs are stale             | `/project:list-docs`             |
| Force-regen all docs now               | `/project:regen-docs`            |
| Rebuild just the architecture diagram  | `/project:doc architecture`      |
| Suggest next unchecked task            | `/project:next`                  |
| Breathe extra brain-cycles into Claude | `/user:think-hard <your prompt>` |

---

## 🤔 FAQ

**Q: My monorepo has many packages—can each one have its own README and CLAUDE.md?**

> Yep. Doc-keeper treats `packages/*/README.md` and `packages/*/CLAUDE.md` as “ask-before-update” docs.
> Add package-specific tech-stack notes there and Claude will respect them.

**Q: What if I hate Mermaid?**

> Swap the guideline in `CLAUDE.md` to PlantUML or draw\.io links—Doc-keeper will follow your rule.

**Q: Does this work with Sonnet?**

> It’ll work—just slower and dumber. Run `/model opus` once; worth the extra pennies.

---

## 🥳 Contribute

PRs welcome! Ideas:

- Add a Bun-native CI template.
- Include a Playwright smoke-test step.
- More custom commands? PR the prompt file.

Just keep prompts **short & snappy**—Claude likes imperative prose.

---

Happy **plan-test-code-docs** looping! If you ship something cool with this workflow, tweet @you and brag. 🎉
