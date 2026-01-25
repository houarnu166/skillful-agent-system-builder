---
name: agent-design
description: |
  Guide for designing Agent frontmatter and system prompts with proper tool selection.
  Use when: (1) Creating new Agents/Subagents, (2) Defining Agent roles and triggers,
  (3) Selecting tools and permission modes, (4) Writing system prompts.
  Triggers: "agent design", "create agent", "subagent", "agent frontmatter"
---

# Agent Design Guide

## Required Frontmatter Fields

| Field | Format | Rules |
|-------|--------|-------|
| `name` | lowercase + hyphens | 1-64 chars, no reserved words |
| `description` | text | 1-1024 chars, role + trigger conditions |
| `tools` | array | least privilege principle |

→ See [frontmatter-fields.md](references/frontmatter-fields.md) for detailed rules

## Tool Selection Guide

| Pattern | Tools | Purpose |
|---------|-------|---------|
| Read-only | `Read, Grep, Glob, Bash` | Exploration/Analysis |
| Writer | `Read, Write, Edit, Bash` | File modification |
| Interactive | `Read, AskUserQuestion` | User questions |
| Orchestrator | `Read, Write, Task` | Subagent coordination |

→ See [tool-selection.md](references/tool-selection.md) for detailed patterns

## Model Selection

| Model | Use Case | Cost |
|-------|----------|------|
| `haiku` | Fast exploration, simple tasks | ~$0.25/MTok |
| `sonnet` | Balanced choice (default) | ~$3/MTok |
| `opus` | Complex reasoning | ~$15/MTok |

## System Prompt Structure

```markdown
You are {name}, a {specialist type}.

## Your Role
{detailed role description}

## Process
### Step 1: {step name}
{description}

## Output Format
{expected output format}

## Important Notes
- {cautions}
```

→ See [examples.md](references/examples.md) for complete Agent examples

## Design Principles

1. **Single Responsibility**: Each Agent has one clear role
2. **Clear Triggers**: Specify usage timing in description
3. **Least Privilege**: Grant only necessary tools
4. **Specific Process**: Define step-by-step tasks clearly

## Common Mistakes

- ❌ Missing trigger conditions in description
- ❌ Excessive permission grants (listing all tools)
- ❌ Vague role definition
- ❌ Skipping process steps
- ❌ Giving Task tool to Worker Agents
