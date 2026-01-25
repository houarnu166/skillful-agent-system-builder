---
name: anthropic-reference
description: |
  Reference guide for Anthropic official specifications on Skills and Subagents.
  Use when: (1) Verifying naming conventions, (2) Checking frontmatter field requirements,
  (3) Validating permission modes, (4) Understanding progressive disclosure patterns.
  Triggers: "anthropic guide", "official docs", "naming rules", "field requirements"
---

# Anthropic Official Guide Reference

This Skill provides bundled core content from Anthropic official guidelines.

---

## Skills Guide

→ See [skills-guide.md](references/skills-guide.md) for complete reference

**Core Rules**:

| Field | Constraints |
|-------|-------------|
| `name` | 1-64 chars, lowercase + hyphens, matches directory name |
| `description` | 1-1024 chars, functionality + usage timing |

**Progressive Disclosure**:
1. Metadata (~100 tokens) - Always loaded
2. Instructions (< 5000 tokens) - When activated
3. Resources - When needed

---

## Subagents Guide

→ See [subagents-guide.md](references/subagents-guide.md) for complete reference

**Core Fields**:

| Field | Description |
|-------|-------------|
| `name` | Unique identifier in lowercase + hyphens |
| `description` | Claude auto-delegation trigger |
| `tools` | Allowed tools list |
| `model` | haiku/sonnet/opus/inherit |
| `permissionMode` | Permission mode |

**Permission Modes**:
- `default` - Standard confirmation
- `acceptEdits` - Auto-accept edits
- `plan` - Read-only
- `bypassPermissions` - Skip all confirmations (caution)

---

## Naming Rules

→ See [naming-rules.md](references/naming-rules.md) for validation rules

**Common Rules**:
- 1-64 characters
- Lowercase + numbers + hyphens only
- No hyphen at start/end
- No consecutive hyphens
- No reserved words: `anthropic`, `claude`

**Validation**:
```bash
[[ "$name" =~ ^[a-z][a-z0-9-]*[a-z0-9]$ ]] && [ ${#name} -le 64 ]
```

---

## Quick Reference

### Skill Frontmatter
```yaml
---
name: skill-name
description: |
  What it does. Use when: (1) scenario, (2) scenario.
  Triggers: "keyword1", "keyword2"
---
```

### Agent Frontmatter
```yaml
---
name: agent-name
description: |
  Role description.
  Trigger: Use when condition.
tools: [Read, Grep, Glob]
model: sonnet
permissionMode: default
---
```

---

## Validation Scenarios

### name Validation
1. Lowercase + hyphens only?
2. 1-64 characters?
3. No hyphen at start/end?
4. No consecutive hyphens?
5. No reserved words?
6. (Skill) Matches directory name?

### description Validation
1. 1-1024 characters?
2. Includes functionality description?
3. Includes trigger conditions?
4. Includes keywords?

### Tool Permission Validation
1. Least privilege principle?
2. No Write/Edit for Read-only Agents?
3. No Task for Worker Agents?

---

## Bundled References

All guidelines for this Skill are self-contained:

- [skills-guide.md](references/skills-guide.md) - Skills official guide core
- [subagents-guide.md](references/subagents-guide.md) - Subagents official guide core
- [naming-rules.md](references/naming-rules.md) - Naming rules summary
