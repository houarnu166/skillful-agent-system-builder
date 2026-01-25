# Subagents Official Guide Essentials

## Subagent Definition

A Subagent is a specialized AI assistant that handles specific tasks. Each Subagent:
- Runs in its own context window
- Has a custom system prompt
- Has specific tool access
- Has independent permissions

---

## Frontmatter Fields

| Field | Required | Description |
|-----|-----|------|
| `name` | Yes | Lowercase and hyphen unique identifier |
| `description` | Yes | Describes when Claude should delegate |
| `tools` | No | List of allowed tools |
| `model` | No | Model to use (haiku/sonnet/opus/inherit) |
| `permissionMode` | No | Permission mode |
| `skills` | No | List of Skills to load at startup |
| `hooks` | No | Event hook configuration |
| `color` | No | UI background color |

---

## name Field Rules

- Lowercase + hyphens only
- Unique identifier
- No hyphen at start/end
- Reserved words not allowed: "anthropic", "claude"

---

## description Field

Used by Claude for auto-delegation decisions.

**Good Example**:
```yaml
description: |
  Expert code reviewer. Reviews code for quality, security, and best practices.
  Trigger: Use proactively after code changes or when user requests code review.
```

---

## tools Field

Available tools:
- `Read` - Read files
- `Write` - Write files
- `Edit` - Edit files
- `Grep` - Text search
- `Glob` - File pattern search
- `Bash` - Execute commands
- `AskUserQuestion` - Ask user questions
- `Task` - Call Subagent (for Orchestrator)

**Format**:
```yaml
tools: [Read, Grep, Glob, Bash]
# or
tools: Read, Grep, Glob, Bash
```

---

## model Field

| Value | Description |
|---|------|
| `haiku` | Fast exploration, simple tasks (~$0.25/MTok) |
| `sonnet` | Balanced choice, default (~$3/MTok) |
| `opus` | Complex reasoning (~$15/MTok) |
| `inherit` | Same model as parent |

---

## permissionMode Field

| Mode | Description |
|-----|------|
| `default` | Standard permission check |
| `acceptEdits` | Auto-accept file edits |
| `plan` | Read-only (planning mode) |
| `dontAsk` | Reject permission prompts |
| `bypassPermissions` | Skip all confirmations (Caution!) |

---

## skills Field

List of Skills to load at startup.

```yaml
skills: [skill-a, skill-b]
```

**Note**: Subagents do not inherit parent's Skills.

---

## File Structure

```markdown
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---

You are a code reviewer. When invoked, analyze the code and provide
specific, actionable feedback on quality, security, and best practices.
```

---

## Storage Locations

| Location | Scope | Priority |
|-----|------|---------|
| `--agents` CLI flag | Current session | 1 (Highest) |
| `.claude/agents/` | Current project | 2 |
| `~/.claude/agents/` | All projects | 3 |
| Plugin `agents/` | Where plugin is active | 4 (Lowest) |

---

## Built-in Subagents

| Name | Model | Use Case |
|-----|------|------|
| Explore | Haiku | Codebase search/analysis (read-only) |
| Plan | Inherit | Research for planning |
| General-purpose | Inherit | Complex multi-step tasks |
