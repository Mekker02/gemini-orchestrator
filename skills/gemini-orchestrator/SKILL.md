---
name: gemini-orchestrator
description: >
  Claude acts as orchestrator; delegates subtasks to Gemini CLI as worker agents via Bash.
  Use when user says "use gemini as agent", "orchestrate with gemini", "/gemini-agent",
  or wants multi-LLM task delegation where Claude plans and Gemini executes subtasks.
license: MIT
---

# Gemini Orchestrator

Claude = orchestrator (plan, decompose, synthesize, file edits, final decisions).
Gemini CLI = worker agents (research, large-context reads, code drafts, secondary review).

## Activation

Trigger on: "use gemini", "gemini agent", "delegate to gemini", `/gemini-agent`

## Prerequisites

```bash
which gemini   # must exist
gemini --version
```

If not found: `npm install -g @google/gemini-cli`

## Orchestration Pattern

```
User request
  → Claude decomposes into subtasks
  → Claude dispatches each via: gemini -p "<task prompt>"
  → Claude collects + synthesizes outputs
  → Claude takes final actions (Edit/Write/git)
```

## Dispatch Template

```bash
gemini -p "<task>. Context: <relevant context>. Return: <expected output format>."
```

## Task Routing

| Task type | Route to |
|-----------|----------|
| Large file analysis / summarization | Gemini |
| Web research | Gemini |
| Code draft (first pass) | Gemini |
| Secondary code review | Gemini |
| File edits | Claude (Edit tool) |
| Architectural decisions | Claude |
| Tool calls (Supabase, git, etc.) | Claude |
| Final synthesis | Claude |

## Execution Flow

1. **Decompose** — Claude breaks request into parallel/sequential subtasks
2. **Dispatch** — Claude calls `gemini -p "..."` via Bash for delegated tasks
3. **Collect** — Claude reads Gemini outputs
4. **Synthesize** — Claude merges, resolves conflicts, decides
5. **Act** — Claude applies changes with Edit/Write tools

## Example

User: "Analyze the mockup HTML and suggest component structure"

```bash
# Claude dispatches to Gemini:
gemini -p "Read this HTML mockup and extract all distinct UI components. List each with: name, props, child components, state needs. Be exhaustive. HTML: $(cat drafts/mockup.html)"
```

Claude receives list → makes architectural decisions → creates component files.

## Parallel Dispatch

For independent subtasks, dispatch multiple Gemini calls:

```bash
# Run in background, collect after
gemini -p "Task A..." > /tmp/gemini_a.txt &
gemini -p "Task B..." > /tmp/gemini_b.txt &
wait
cat /tmp/gemini_a.txt /tmp/gemini_b.txt
```

## Limits

- Gemini output = input to Claude decision, not final answer
- Claude always reviews Gemini output before acting
- File writes always go through Claude Edit/Write tools
- Never let Gemini output execute directly as shell commands
