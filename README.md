# Wazuh Hub: Software Engineer Technical Task

Four phases, from a Wazuh lab to a service design and a plan to build it with AI agents. My answers to the Wazuh Hub Software Engineer technical task.

## Deliverables

| Phase | What it contains |
|---|---|
| [Phase&nbsp;1](phase1/PHASE1.md) | My Wazuh lab: installation, agent, admin password change, FIM, my own decoder and rule, SCA, API, and 12 screenshots |
| [Phase&nbsp;2](phase2/PHASE2.md) | The needs I found, what I discarded, the one I chose and why. Sources and notes in [RESEARCH.md](phase2/research/RESEARCH.md) |
| [Phase&nbsp;3](phase3/PHASE3.md) | Design of the service: 6 diagrams with their Mermaid sources in [diagrams](phase3/diagrams), the [evaluation cases](phase3/evaluation/CASES.md), and the lab checks in [evidence](phase3/evidence) |
| [Phase&nbsp;4](phase4/PHASE4.md) | How I worked with AI and how I would build the service with agents: 2 diagrams with their Mermaid sources in [diagrams](phase4/diagrams), and the [prompts](phase4/agent-direction/prompts.md) that did the most to direct the AI |

## Lab at a glance

- **Wazuh:** 4.14.9, Docker, single-node (manager, indexer and dashboard).
- **Agent:** Ubuntu 24.04 on OrbStack, same version as the manager.
- **Screenshots:** [phase1/assets/img](phase1/assets/img)
- **Real API responses:** [phase2/evidence](phase2/evidence)
- **Phase 3 lab checks:** [phase3/evidence](phase3/evidence)

## Repository map

```text
.
├── README.md      # This file
├── PLAN.md        # Day-by-day plan for the 72 hours
├── CLAUDE.md      # Rules for the agents working in this repository
├── phase1/
│   ├── PHASE1.md  # Phase 1 answers
│   ├── agent-fim-config.xml  # FIM block added to the agent's ossec.conf
│   └── assets/img/           # Lab screenshots
├── phase2/
│   ├── PHASE2.md  # Phase 2 answers
│   ├── research/  # RESEARCH.md: sources, notes and discarded needs
│   └── evidence/  # Real Wazuh API responses, decoder and rule files
├── phase3/
│   ├── PHASE3.md  # Phase 3 answers
│   ├── diagrams/  # Mermaid sources (.mmd) of the 6 diagrams
│   ├── evaluation/  # CASES.md: cases to evaluate the service
│   └── evidence/  # Lab checks: sandbox, regression tests, API permissions
└── phase4/
    ├── PHASE4.md  # Phase 4 answers
    ├── diagrams/  # Mermaid sources (.mmd) of the 2 diagrams
    └── agent-direction/  # The prompts that directed the AI
```
