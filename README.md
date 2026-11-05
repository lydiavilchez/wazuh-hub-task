# Wazuh Hub: Software Engineer Technical Task

This repository holds my work for the Wazuh Hub Software Engineer technical task. 

## Deliverables

| Phase | What it contains | Status |
|---|---|---|
| [Phase 1](phase1/PHASE1.md) | My Wazuh lab: installation, agent, admin password change, FIM, my own decoder and rule, SCA, API, and 12 screenshots | Done |
| [Phase 2](phase2/PHASE2.md) | The needs I found, what I discarded, the one I chose and why. Sources and notes in [RESEARCH.md](phase2/research/RESEARCH.md) | Done |
| Phase 3 | Service design and diagrams | In progress |
| Phase 4 | Plan to build it with AI agents | In progress |

## Lab at a glance

- **Wazuh:** 4.14.9, Docker, single-node (manager, indexer and dashboard).
- **Agent:** Ubuntu 24.04 on OrbStack, same version as the manager.
- **Screenshots:** [phase1/assets/img](phase1/assets/img)
- **Real API responses:** [phase2/evidence](phase2/evidence)

## Repository map

```text
.
├── README.md      # This file
├── PLAN.md        # Day-by-day plan for the 72 hours
├── phase1/
│   ├── PHASE1.md  # Phase 1 answers
│   ├── agent-fim-config.xml  # FIM block added to the agent's ossec.conf
│   └── assets/img/           # Lab screenshots
└── phase2/
    ├── PHASE2.md  # Phase 2 answers
    ├── research/  # RESEARCH.md: sources, notes and discarded needs
    └── evidence/  # Real Wazuh API responses, decoder and rule files
```
