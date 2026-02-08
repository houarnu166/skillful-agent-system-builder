# Skills Library

Module Context: Central registry for all available skills. Referenced by agents during execution.

## Skill Categories

### Agent Design & Patterns

| Skill | Purpose | Used By |
|-------|---------|---------|
| [agent-design](./agent-design/SKILL.md) | Agent frontmatter specification, role definition | Jenny, Will, Tom |
| [skill-design](./skill-design/SKILL.md) | Skill YAML structure, progressive disclosure | Jenny, Will, Tom |
| [autonomous-agent-patterns](./autonomous-agent-patterns/SKILL.md) | Self-correcting agent architectures | Jenny |
| [agent-memory-systems](./agent-memory-systems/SKILL.md) | Persistent state, context management | Jenny |

### Quality & Validation

| Skill | Purpose | Used By |
|-------|---------|---------|
| [quality-checklist](./quality-checklist/SKILL.md) | Validation checklist, issue detection | Will |
| [agent-evaluation](./agent-evaluation/SKILL.md) | Performance metrics, testing strategies | Will |
| [verification-before-completion](./verification-before-completion/SKILL.md) | Pre-output validation | Will |

### Planning & Architecture

| Skill | Purpose | Used By |
|-------|---------|---------|
| [concise-planning](./concise-planning/SKILL.md) | Efficient requirement analysis | Sam |
| [prompt-engineering](./prompt-engineering/SKILL.md) | Prompt optimization techniques | Jenny, Will |
| [multi-agent-brainstorming](./multi-agent-brainstorming/SKILL.md) | Collaborative design patterns | Jenny (optional) |
| [context-window-management](./context-window-management/SKILL.md) | Token efficiency strategies | Jenny, Will |

### Reference & Documentation

| Skill | Purpose | Used By |
|-------|---------|---------|
| [anthropic-reference](./anthropic-reference/SKILL.md) | Official Anthropic guidelines | Jenny, Will |
| [documentation-templates](./documentation-templates/SKILL.md) | Output formatting templates | Tom |

### Tools & Infrastructure

| Skill | Purpose | Used By |
|-------|---------|---------|
| [agent-tool-builder](./agent-tool-builder/SKILL.md) | Custom tool creation | Jenny |
| [mcp-builder](./mcp-builder/SKILL.md) | MCP server setup | Jenny |
| [project-scaffolding](./project-scaffolding/SKILL.md) | File structure generation | Tom |
| [skill-creator](./skill-creator/SKILL.md) | New skill template generation | Jenny |

---

## Skill Usage Patterns

### During Requirements (Sam):
```
Skills: concise-planning
Trigger: New user request analysis
Purpose: Extract requirements without over-engineering
```

### During Design (Jenny):
```
Skills: agent-design, skill-design, anthropic-reference
Trigger: Agent/skill specification creation
Purpose: Generate compliant frontmatter and system prompts

Optional Skills (activate if needed):
- multi-agent-brainstorming: Complex multi-agent systems
- agent-memory-systems: Persistent state requirements
- autonomous-agent-patterns: Self-correcting behaviors
```

### During Validation (Will):
```
Skills: quality-checklist, verification-before-completion
Trigger: Pre-output quality gate
Purpose: Detect issues, classify by category, apply fixes
```

### During Output (Tom):
```
Skills: project-scaffolding, documentation-templates
Trigger: Final output generation
Purpose: Generate Start Prompt and project files
```

---

## Skill File Structure

Each skill follows YAML frontmatter format:

```yaml
---
name: skill-name
description: When and why to use this skill
---

# Skill Name

[Detailed instructions, patterns, examples]
```

**Required Sections**:
- Purpose/trigger conditions
- Core patterns or rules
- Examples (if applicable)

**Character Limit**: Description field max 1024 chars

---

## Adding New Skills

1. Create directory: `.agents/skills/{skill-name}/`
2. Create file: `SKILL.md`
3. Add YAML frontmatter with name and description
4. Document usage patterns
5. Register in this file under appropriate category
6. Update agent definitions that should use the skill
