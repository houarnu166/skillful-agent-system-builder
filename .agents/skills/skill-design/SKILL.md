---
name: skill-design
description: |
  Guide for designing SKILL.md files with bundled resources and progressive disclosure.
  Use when: (1) Creating new Skills, (2) Reviewing Skill quality, (3) Refactoring existing
  Skills for better context efficiency.
  Triggers: "create skill", "skill design", "SKILL.md"
---

# Skill Design Guide

## Overview

This Skill provides methods for designing and implementing effective Skills. Skills are modular packages that extend Claude's capabilities for specific domains or tasks.

## About Skills

Skills are modular, self-contained packages that extend Claude's capabilities by providing specialized knowledge, workflows, and tools. Think of them as **"onboarding guides"** for specific domains or tasks.

**What Skills provide**:
1. **Specialized workflows** - Multi-step procedures for specific domains
2. **Tool integrations** - Guides for specific file formats or API operations
3. **Domain expertise** - Company-specific knowledge, schemas, business logic
4. **Bundled resources** - Scripts, reference documents, assets for complex and repetitive tasks

## Core Principle: Concise is Key

**Context window is a public good.** Only add context Claude doesn't already know.

- "Does Claude really need this explanation?"
- "Does this paragraph justify its token cost?"

**Prefer concise examples over verbose explanations.**

---

## Skill Creation Process (6 Steps)

### Step 1: Understand with Concrete Examples
Collect and understand concrete examples of how the Skill will be used.

### Step 2: Plan Reusable Content
Identify reusable resources that can be separated into scripts, references, assets.

### Step 3: Initialize Skill
Create Skill directory structure. (Recommend using `init_skill.py` script)

### Step 4: Write Skill
Implement SKILL.md and bundled resources.
→ See [frontmatter-guide.md](references/frontmatter-guide.md) for frontmatter rules

### Step 5: Package Skill
Package into distributable .skill file. (Use `package_skill.py`)

### Step 6: Iterate with Real Usage
Improve based on actual usage.

---

## SKILL.md Structure

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (name, description)
│   └── Markdown instructions
└── Bundled Resources (optional)
    ├── scripts/          - Executable code
    ├── references/       - Documents loaded when needed
    └── assets/           - Files used in output
```

### Frontmatter Essentials

| Field | Rules |
|-------|-------|
| `name` | lowercase + hyphens, 1-64 chars, matches directory name |
| `description` | 1-1024 chars, includes functionality + trigger conditions |

**Important**: Include all "when to use" info in description. "When to Use" sections in Body don't help Claude since they're only loaded after triggering.

→ See [frontmatter-guide.md](references/frontmatter-guide.md) for detailed rules

---

## Progressive Disclosure (3-Level Loading)

1. **Metadata** (~100 words) - Always in context
2. **SKILL.md body** (<500 lines) - When Skill triggers
3. **Bundled resources** - Loaded as needed

### 3 Patterns

| Pattern | When to Use |
|---------|-------------|
| **Pattern 1**: High-level Guide | When there are multiple related features |
| **Pattern 2**: Domain-specific | When there are distinct sub-domains |
| **Pattern 3**: Conditional Details | When advanced features are occasionally needed |

→ See [progressive-disclosure.md](references/progressive-disclosure.md) for patterns and examples

---

## Bundled Resources

| Directory | Purpose | Example |
|-----------|---------|---------|
| `scripts/` | Code requiring deterministic reliability | `rotate_pdf.py` |
| `references/` | Documents loaded when needed | `schema.md` |
| `assets/` | Files used in output | `template.html` |

→ See [bundled-resources.md](references/bundled-resources.md) for detailed guide

---

## Decision Tree: Content Placement

```
Content Type → Placement
├─ Trigger conditions → description (frontmatter)
├─ Core workflow → SKILL.md body
├─ Detailed reference → references/
├─ Repetitive code → scripts/
└─ Output templates → assets/
```

---

## Design Principles

1. **Conciseness**: SKILL.md core only, under 500 lines
2. **Progressive Disclosure**: Utilize 3-level loading
3. **Clear Triggers**: Specify usage timing in description
4. **1 Level Depth**: References link directly from SKILL.md

---

## Validation

→ See [validation-checklist.md](references/validation-checklist.md) for complete checklist

**Quick Check**:
- [ ] `name`: lowercase + hyphens, 1-64 chars
- [ ] `description`: functionality + trigger conditions, 1-1024 chars
- [ ] SKILL.md: under 500 lines
- [ ] No unnecessary files (README.md, CHANGELOG.md, etc.)

---

## Common Mistakes

- ❌ Missing trigger conditions in description
- ❌ Including "When to Use" section in Body
- ❌ SKILL.md over 500 lines
- ❌ References nested 2+ levels deep
- ❌ Information duplicated between SKILL.md and references
