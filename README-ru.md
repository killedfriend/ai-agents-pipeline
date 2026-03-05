# Multi-Agent Development Pipeline

Автоматизированный pipeline из 5 специализированных Claude-агентов.

## Схема

```
[Бизнес-требования]
       ↓
  Agent #1: BRD Writer          → docs/brd/{feature}.md
       ↓                          (reads MEMORY.md first)
  Agent #2: Tech Planner        → docs/specs/{feature}.md
       ↓
  Agent #3: Task Distributor
  ├── #3a Frontend Agent        → src/
  ├── #3b Backend Agent         → api/
  └── #3c Infrastructure Agent  → migrations/, docker-compose
       ↓ (все три параллельно)
  Agent #4: Deployer            → SSH → test server
       ↓
  Agent #5: QA Tester           → Playwright + API tests
  ├── Fail → Back to Agent #3 (max 3 attempts)
  └── Pass → Agent #6
       ↓
  Agent #6: Memory Updater      → MEMORY.md + commit to main
       ↓
  [Цикл замкнулся — Agent #1 готов к следующей задаче]
```

## Структура

```
.pipeline/
├── README.md           # This file
├── config.json         # Pipeline configuration (fill in your values)
├── agents/             # System prompts for each agent
│   ├── brd-writer.md
│   ├── tech-planner.md
│   ├── implementers.md
│   ├── deployer.md
│   ├── qa-tester.md
│   └── memory-updater.md
├── state/
│   ├── current.json    # Current pipeline run state
│   └── history/        # Past run states
└── patches/            # Bug reports from QA → #3
```

## Запуск

1. Заполни `config.json` актуальными значениями
2. Установи env переменные: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`
3. Запусти Agent #1 с бизнес-требованиями:

```
Прочитай .pipeline/agents/brd-writer.md и действуй согласно инструкциям.

Бизнес-требования:
[твои требования здесь]
```

4. Передавай каждому следующему агенту его system prompt + текущее состояние из `.pipeline/state/current.json`

## Фазы реализации pipeline

- [x] Phase 1: Структура директорий и system prompts агентов
- [ ] Phase 2: Настройка тестового сервера + SSH
- [ ] Phase 3: Настройка Telegram Bot
- [ ] Phase 4: Playwright тесты (smoke)
- [ ] Phase 5: Первый E2E прогон pipeline
