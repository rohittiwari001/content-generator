# How Cadence works — a beginner's reference

Notes on how this project uses Claude Code's multi-agent features, so you can build your own workflows the same way.

---

## The core building blocks

### 1. Slash commands (`.claude/commands/*.md`)

Plain markdown files. When you type `/run-pipeline`, Claude Code loads that file's contents as an instruction prompt for the *current* conversation. There's no special syntax — it's literally "here is your job, follow these steps." `$ARGUMENTS` gets substituted with whatever you typed after the command.

### 2. Sub-agents (`.claude/agents/*.md`)

Also markdown, but these are *not* run in your main conversation. They're specs for a fresh, isolated Claude session — spawned via the `Agent` tool (sometimes called "Task"). Each one declares its own frontmatter:

```yaml
---
description: Research Agent — finds trending topics...
model: haiku
---
```

`model: haiku` means Anthropic runs cheap/fast searches on Haiku, but `hook-factory`, `content-writer`, `style-editor`, `strategy-analyzer` use `sonnet` for anything requiring real writing judgment. That's a deliberate cost/quality tradeoff you can copy: cheap model for mechanical search/filter work, better model for anything creative or nuanced.

### 3. The orchestrator pattern

`/run-pipeline` (the command you type) is the *only* agent that talks to you. It never writes posts itself — it just calls sub-agents in sequence, passes results between them, and shows you clean output. The flow in `run-pipeline.md`:

```
researcher.md          → 10-12 trending topics (raw search noise stays inside this agent)
     ↓
topic-ranker.md        → merges research + your own ideas, ranks top 8
     ↓
[CHECKPOINT: you pick topics]
     ↓
topic-deep-researcher.md → spawned ONCE PER TOPIC, IN PARALLEL
     ↓
hook-factory.md         → 5 hook options per topic
     ↓
[CHECKPOINT: you pick a hook per topic]
     ↓
content-writer.md       → full drafts, reads the whole knowledge base first
     ↓
style-editor.md         → polish pass against a strict checklist
     ↓
[CHECKPOINT: you approve/revise/skip each post]
     ↓
publish (Bash script) + log to Notion (MCP) + append to strategy_log.md
```

**Why isolate agents at all?** Each sub-agent gets its own context window. The researcher does 6 messy web searches — you don't want that noise cluttering the main conversation. Only the structured JSON result (`{"topics": [...]}`) comes back to the orchestrator. This is the single biggest lesson for building your own workflows: **push exploratory/noisy work into a sub-agent, and design a strict JSON contract for what comes back.**

### 4. Checkpoints = deliberate pauses for human approval

The orchestrator is scripted to *stop* and ask a specific question, wait for a literal answer (e.g., "1,3,5"), and only then proceed. This is enforced entirely by prompt instructions, not code — the model just follows the written steps faithfully.

### 5. Knowledge base as shared long-term memory

`knowledge_base/*.md` files aren't code — they're persistent context that every agent is told to `Read` before doing its job (voice samples, rules, profile). This is how all agents "agree" on the same voice without literally talking to each other — they all read the same source files.

---

## How to build your own version

1. Write one orchestrator command (`/my-pipeline`) that never does the actual work — just sequences steps and talks to the user.
2. Write one `.md` file per sub-task under `.claude/agents/`, each with a narrow job, explicit input, and a strict output format (JSON is easiest to pass between agents).
3. Pick `model: haiku` for mechanical/search steps, `sonnet`/`opus` for judgment-heavy steps.
4. Decide where parallel agents make sense (independent per-item work, like the per-topic deep-researcher) vs. sequential (each step depends on the last).
5. Put anything that needs to persist across runs (voice, rules, history) into plain files the agents are instructed to read — that's your "memory," no database needed.
6. Insert explicit checkpoints anywhere an irreversible action (like publishing) is downstream.
