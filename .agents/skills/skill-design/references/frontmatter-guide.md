# SKILL.md Frontmatter Guide

YAML frontmatter contains `name` and `description` fields. **These are the only fields Claude reads to decide when to use a skill.**

## name Field

- **Format**: lowercase + hyphens
- **Length**: 1-64 characters
- **Constraints**:
  - Must match directory name
  - Reserved words not allowed: "anthropic", "claude"
  - No hyphen at start/end
  - No consecutive hyphens
- **Examples**: `pdf-editor`, `data-analyst`, `brand-guidelines`

## description Field

- **Length**: 1-1024 characters
- **Composition**: What the Skill does + specific trigger/context
- **Important**: **Include all "when to use" information here** - Do not include in Body. Body is only loaded after trigger

### Good description Example

```yaml
description: |
  Comprehensive document creation, editing, and analysis with support for
  tracked changes, comments, formatting preservation, and text extraction.
  Use when Claude needs to work with professional documents (.docx files) for:
  (1) Creating new documents, (2) Modifying or editing content,
  (3) Working with tracked changes, (4) Adding comments, or any other document tasks
```

### Description Writing Checklist

- [ ] Explain what the Skill does
- [ ] Include specific trigger conditions
- [ ] List usage scenarios (1, 2, 3...)
- [ ] Mention file formats or specific keywords (e.g., .docx, PDF, BigQuery)
- [ ] Keep under 1024 characters

## Recommended Body Content Structure

```markdown
# {Skill Title}

## Overview
{Brief description}

## Core Concepts
{Concepts that need understanding}

## Usage

### Basic Usage
{Basic patterns}

### Advanced Patterns
{Advanced usage}

## Examples

### Example 1: {Scenario}
{Concrete example}

## Notes
- {Points to note}
```

**Recommended Length**: Keep SKILL.md under 500 lines.

## What NOT to Include

Do not create the following files/content:

- ❌ README.md
- ❌ INSTALLATION_GUIDE.md
- ❌ QUICK_REFERENCE.md
- ❌ CHANGELOG.md
- ❌ Other supplementary documents

Skills should only contain information necessary for the AI agent to perform tasks.
