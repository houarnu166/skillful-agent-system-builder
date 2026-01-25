---
name: quality-checklist
description: |
  Quality validation checklist for Agent+Skill systems before deployment.
  Use when: (1) Final design review, (2) Validating naming conventions,
  (3) Checking permission settings, (4) Verifying progressive disclosure structure.
  Triggers: "quality check", "validate design", "review checklist"
---

# Quality Review Checklist

→ See [agent-validation.md](references/agent-validation.md) for Agent details
→ See [skill-validation.md](references/skill-validation.md) for Skill details
→ See [common-issues.md](references/common-issues.md) for troubleshooting

## 1. Structure Review

### Overall System
- [ ] Orchestrator Agent exists
- [ ] All required Worker Agents defined
- [ ] All required Skills defined
- [ ] Workflow logical completeness
- [ ] Error handling logic included

### File Structure
- [ ] `.agents/` directory structure clear
- [ ] `.agents/skills/` directory structure clear
- [ ] temp/ usage plan specified
- [ ] outputs/ usage plan specified

## 2. Agent Review

For each Agent:

### Frontmatter
- [ ] `name`: lowercase, hyphens, 1-64 chars
- [ ] `name`: no hyphen at start/end
- [ ] `name`: no consecutive hyphens
- [ ] `description`: 1-1024 chars
- [ ] `description`: includes role description
- [ ] `description`: trigger conditions clear
- [ ] `tools`: only necessary tools included
- [ ] `tools`: no excessive permissions
- [ ] `model`: appropriate model (haiku/sonnet/opus)
- [ ] `permissionMode`: appropriate permission mode
- [ ] `skills`: referenced skills actually exist

### System Prompt
- [ ] Role clearly defined
- [ ] Call conditions specified (if needed)
- [ ] Process explained step by step
- [ ] Output format defined
- [ ] Examples included (recommended)
- [ ] Cautions specified

## 3. Skill Review

For each Skill:

### Frontmatter
- [ ] `name`: lowercase, hyphens, 1-64 chars
- [ ] `name`: matches directory name
- [ ] `name`: no reserved words (anthropic, claude)
- [ ] `description`: 1-1024 chars
- [ ] `description`: includes functionality description
- [ ] `description`: usage timing clear

### Body Content
- [ ] Overview clear
- [ ] Core concepts explained
- [ ] Usage detailed
- [ ] Examples specific
- [ ] Cautions specified
- [ ] Under 500 lines (recommended)

### Bundled Resources
- [ ] references/ configured if needed
- [ ] scripts/ configured if needed
- [ ] assets/ configured if needed
- [ ] File references accurate
- [ ] 1 level depth maintained

## 4. Best Practices Review

### Progressive Disclosure
- [ ] Metadata: name + description concise
- [ ] Instructions: SKILL.md under 5k tokens
- [ ] Resources: load-on-demand structure

### Least Privilege Principle
- [ ] Each Agent has only necessary tools
- [ ] Read-only Agents have no Write/Edit
- [ ] Only Orchestrator has Task tool

### Clear Triggers
- [ ] All descriptions specify triggers
- [ ] Include "Use when...", "Trigger: ..." etc.
- [ ] Enable Claude auto-delegation

### Orchestrator Mediation
- [ ] No direct calls between Subagents
- [ ] All data transfer via Orchestrator
- [ ] Centralized state management

### Error Handling
- [ ] Handle missing files
- [ ] Handle insufficient permissions
- [ ] Handle user interruption
- [ ] Retry logic (if needed)

## 5. Documentation Review

### Start Prompt
- [ ] System purpose clear
- [ ] File structure provided
- [ ] Usage detailed
- [ ] Example scenarios included
- [ ] Customization guide included

### Workflow
- [ ] Overall flow explained
- [ ] Step-by-step details
- [ ] Scenario examples
- [ ] Diagrams (recommended)

## 6. Quality Standards

### Critical (Required)
All Critical items must pass:
- name/description required fields
- Naming convention compliance
- Clear role/process
- Least privilege principle

### Warning (Recommended)
Most Warning items should pass:
- Examples included
- Progressive disclosure
- Sufficient documentation

### Info (Reference)
Areas for improvement:
- Add diagrams
- More examples
- Customization options

## Review Result Format

```markdown
## 🔍 Review Results

### ✅ Passed Items ({n})
- {item 1}
- {item 2}

### ⚠️ Warning Items ({m})
1. {issue}
   - Current: {status}
   - Recommended: {improvement}

### ❌ Required Fixes ({k})
1. {issue}
   - Current: {status}
   - Fix: {solution}

### 💡 Improvement Suggestions
- {suggestion 1}
- {suggestion 2}
```
