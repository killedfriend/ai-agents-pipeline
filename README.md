# Multi-Agent Development Pipeline

A self-closing automated pipeline of 6 specialized Claude agents — from business requirement to deployed feature with persistent project memory.

## How It Works

```
[Business Requirements]
       ↓
  Agent #1: BRD Writer          → docs/brd/{feature}.md
       ↓                          (reads MEMORY.md first)
  Agent #2: Tech Planner        → docs/specs/{feature}.md
       ↓
  Agent #3: Task Distributor
  ├── #3a Frontend Agent        → src/
  ├── #3b Backend Agent         → api/
  └── #3c Infrastructure Agent  → migrations/, docker-compose
       ↓ (all three in parallel)
  Agent #4: Deployer            → SSH → test server
       ↓
  Agent #5: QA Tester           → Playwright + API tests
  ├── Fail → Back to Agent #3 (max 3 attempts)
  └── Pass → Agent #6
       ↓
  Agent #6: Memory Updater      → MEMORY.md + commit to main
       ↓
  [Loop closed — Agent #1 ready for the next feature]
```

Each agent has a focused role and a system prompt in `agents/`. State is passed between agents via `.pipeline/state/current.json`. After QA passes, Agent #6 updates `MEMORY.md` so Agent #1 always starts the next run with accurate context of what's already built.

## Repository Structure

```
.pipeline/
├── README.md              # This file
├── config.json            # Pipeline configuration
├── agents/                # System prompts for each agent
│   ├── brd-writer.md      # Agent #1
│   ├── tech-planner.md    # Agent #2
│   ├── implementers.md    # Agent #3 (frontend + backend + infra)
│   ├── deployer.md        # Agent #4
│   ├── qa-tester.md       # Agent #5
│   └── memory-updater.md  # Agent #6
├── state/
│   ├── current.json       # Current pipeline run state
│   └── history/           # Archived past runs
└── patches/               # Bug reports from QA → Agent #3
```

## Getting Started

1. Fill in `config.json` with your values (SSH host, URLs, etc.)
2. Set environment variables: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`
3. Start Agent #1 with your business requirements:

```
Read .pipeline/agents/brd-writer.md and follow the instructions.

Business requirements:
[your requirements here]
```

4. Pass each subsequent agent its system prompt + current state from `.pipeline/state/current.json`

## Implementation Phases

- [x] Phase 1: Directory structure and agent system prompts
- [ ] Phase 2: Test server setup + SSH
- [ ] Phase 3: Telegram Bot setup
- [ ] Phase 4: Playwright smoke tests
- [ ] Phase 5: First full E2E pipeline run
