# Agent Frontmatter Fields Reference

## Required Fields

### name
- **Format**: lowercase + hyphens only
- **Length**: 1-64 characters
- **Rules**:
  - No hyphen at start/end
  - No consecutive hyphens
  - Reserved words not allowed: "anthropic", "claude"
- **Examples**: `code-reviewer`, `data-analyst`, `test-runner`, `sam-analyst`

### description
- **Length**: 1-1024 characters
- **Composition**: (1) Role description + (2) Trigger conditions
- **Important**: Include clear trigger conditions so Claude can auto-delegate

**Good Example**:
```yaml
description: |
  Expert code reviewer. Reviews code for quality, security, and best practices.
  Trigger: Use proactively after code changes or when user requests code review.
```

**Bad Example**:
```yaml
description: Code reviewer  # Too short, no trigger conditions
```

### tools
Available tools list:
- `Read` - Read files
- `Write` - Write files
- `Edit` - Edit files
- `Grep` - Text search
- `Glob` - File pattern search
- `Bash` - Execute commands
- `AskUserQuestion` - Ask user questions
- `Task` - Call Subagent (for Orchestrator)

---

## Optional Fields

### model
| Model | Use Case | Cost |
|-----|------|------|
| `haiku` | Fast exploration, simple tasks | ~$0.25/MTok |
| `sonnet` | Balanced choice, default | ~$3/MTok |
| `opus` | Complex reasoning | ~$15/MTok |
| `inherit` | Same model as parent | - |

### permissionMode
| Mode | Description |
|-----|------|
| `default` | Standard permission check |
| `acceptEdits` | Auto-accept file edits |
| `plan` | Read-only (planning mode) |
| `dontAsk` | Reject permission prompts |
| `bypassPermissions` | Skip all confirmations (**Caution!**) |

### skills
- List of Skills to load at startup
- Subagents do not inherit parent's Skills
- Array format: `skills: [skill-a, skill-b]`

---

## Complete Frontmatter Example

```yaml
---
name: code-reviewer
description: |
  Expert code reviewer. Reviews code for quality, security, and best practices.
  Trigger: Use proactively after code changes or when user requests code review.
tools: [Read, Grep, Glob, Bash]
model: sonnet
permissionMode: default
skills: [security-checklist]
---
```
