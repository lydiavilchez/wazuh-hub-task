# CLAUDE.md

Instructions for Claude Code and any other agent working in this repository. They collect the rules I followed with my agents during the task, written down at the end of it.

## What this repo is

My work for the Wazuh Hub technical task. No service is built: the repo holds research, a design and lab evidence.

| Phase | Content | Folder |
|---|---|---|
| 1 | Lab and first detections | `phase1/` |
| 2 | Needs research and choice of need | `phase2/` |
| 3 | Service design, diagrams, lab checks, evaluation cases | `phase3/` |
| 4 | How I worked with AI and the agent plan for the service | `phase4/` |

The lab is Wazuh 4.14.9 in Docker (single-node) with an Ubuntu 24.04 agent on OrbStack.

## How a statement gets into a document

- Every technical claim needs a source link or a result measured in my lab.
- Mark each claim as one of: checked in my lab, proposal, to be checked.
- Do not present a planned test as executed.
- Do not describe another product's internals beyond what its public docs say.

## Writing

- All repo text is in English. Spanish drafts (`*_es.md`) are not committed.
- First person, plain and short sentences. No filler, no inflated claims, no emojis or badges.
- When reviewing, fix errors only. Do not change the content unless I ask.

## Hard rules

- Phase 1 files are final. Do not edit anything in `phase1/`.
- Never print or commit secrets, tokens, passwords or personal data. If you need the API password, ask me to type it with `read -rs` and never write it to a file. Before saving evidence, search it for `eyJ`, `password` and `Bearer`.
- Against my lab, read-only calls only, unless I ask for a specific change. Before touching my real Wazuh, tell me in one sentence what you will do and wait for my OK.
- Tests go in a new container with `--network none`. Do not use my docker-compose or touch my containers.
- Remove any test user, role, policy, file or container created for a check, and confirm it is gone.
- Show me the plan and wait for my OK before moving or deleting files. Use `git mv` to keep history.

## Git

- Work on `main` unless I ask for a branch.
- Commit or push only when I ask. Check `git status` before every commit.
- Message format: `docs(scope): short description`. 

## Before a document is called done

- Relative links point to files that exist. External links open, or are listed as unreachable with the reason.
- Mermaid diagrams render, and each has its `.mmd` source in the repo.
- Numbers come from a measurement or a cited source.
