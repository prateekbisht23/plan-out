# plan-out

A skill for Claude that plans big features as **small, scoped spec modules**, keeps them consistent as plans change, and cleans up docs folders that have drifted into contradiction.

## The problem

Plan a feature over several AI sessions and you end up with a pile of files that are each internally consistent but disagree with each other. Session 3 changes a decision from session 1, nothing updates session 1's file, and both claim to be true. Asking an AI to reconcile them makes it worse: it can't tell the current decision from a superseded one, so it guesses, blends, and invents.

## How plan-out prevents it

- **One owner per fact.** Every fact lives in exactly one module; everything else links to it.
- **A decision log with status.** Each decision has an id, an owner, and a status of `active` or `superseded`. Changes supersede, never overwrite.
- **Specs and tasks stay separate.** Modules are the lasting documentation. Tasks are derived from them, link back one-way, and are archived when done.
- **A script is the referee.** `check.py` mechanically catches broken links, stale decision references, duplicate definitions, uncovered sections and more, so consistency does not depend on an AI's impression.
- **Never assume.** Unknowns become batched questions with recommended answers. Skipped questions are logged as explicit defaults.
- **Cleanup is extractive.** Existing docs are moved verbatim into the new structure, conflicts are decided by you, and a ledger proves nothing was lost or invented.

## What it does

| You say | It does |
| --- | --- |
| "Plan a feature that lets users upload files" (vague or detailed) | Plays back its understanding, asks clarifying questions in small batches, proposes modules for approval, then writes modules, a decision log and a task list |
| "Change the upload limit to 25 MB" | Finds the owning module, shows the impact, supersedes the old decision, edits only what must change, flags affected tasks |
| "Our docs contradict each other, clean them up" | Inventories, extracts claims, shows you each conflict, migrates verbatim on a git branch, verifies with a ledger, then deletes the old files |

The result is docs a new engineer can learn the codebase from, plus a task list an AI agent can implement without asking anyone anything.

## Output layout

```
docs/
  README.md        generated index
  modules/         one file per module (the lasting spec)
  decisions.md     decision log
  tasks/           active task lists
    archive/       finished work, frozen
```

See [`examples/sample-docs`](examples/sample-docs) for a small worked example that passes the checker.

## Install

### Claude Code (recommended)

Run these inside Claude Code:

```
/plugin marketplace add prateekbisht23/plan-out
/plugin install plan-out@plan-out
```

The first command registers this repo as a marketplace. The second installs the plugin. To update later: `/plugin marketplace update plan-out`.

Or from your shell, without opening a session:

```
claude plugin marketplace add prateekbisht23/plan-out
claude plugin install plan-out@plan-out
```

Once installed you don't need to invoke anything. The skill triggers on its own when you talk about planning a feature, changing a plan, or cleaning up docs.

### Manual install (no plugin system)

Copy the skill folder into a skills directory:

```
git clone https://github.com/prateekbisht23/plan-out
cp -r plan-out/plugins/plan-out/skills/plan-out ~/.claude/skills/plan-out      # all projects
# or: <your-project>/.claude/skills/plan-out                                  # one project
```

### Claude.ai

Download `plan-out.skill` from the latest [release](../../releases) and upload it in the skills section of your settings.

Cleanup and update modes edit files and use git, so they work best in Claude Code or any environment with filesystem access. In a plain chat window the skill can plan and draft but cannot touch your repo.

Install commands and menu names change between Claude Code versions; if something doesn't match, see Anthropic's plugin documentation.

## The checker

Python 3.8+, standard library only.

```
python plugins/plan-out/skills/plan-out/scripts/check.py check   --docs docs        # all consistency checks
python plugins/plan-out/skills/plan-out/scripts/check.py impact  storage#retention  # what depends on this section
python plugins/plan-out/skills/plan-out/scripts/check.py archive <feature>          # finish a feature
python plugins/plan-out/skills/plan-out/scripts/check.py index                      # regenerate docs/README.md
python plugins/plan-out/skills/plan-out/scripts/check.py ledger  .plan-out/ledger.json   # verify a cleanup
```

## Repo layout

```
.claude-plugin/marketplace.json     makes this repo installable as a Claude Code marketplace
plugins/plan-out/
  .claude-plugin/plugin.json        plugin manifest
  skills/plan-out/                  the skill (SKILL.md, references/, assets/templates/, scripts/)
examples/                           sample output
evals/                              test prompts for developing the skill
```

## License

MIT, see [LICENSE](LICENSE).
