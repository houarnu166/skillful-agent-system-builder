# Progressive Disclosure Patterns

Skills use a 3-level loading system to manage context efficiently.

## 3-Level Loading System

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - When Skill is triggered (<5k words, recommended 500 lines)
3. **Bundled resources** - When Claude needs them (unlimited - scripts can be executed without loading into context)

## Core Principle

When a Skill supports multiple variations, frameworks, or options, keep only the core workflow and selection guide in SKILL.md. Move variation-specific details to separate reference files.

---

## Pattern 1: High-level Guide with References

**When to Use**: When there are multiple related features

**Example Structure**:
```markdown
# PDF Processing

## Quick start
Extract text with pdfplumber:
[code example]

## Advanced features
- **Form filling**: See [FORMS.md](references/FORMS.md) for complete guide
- **API reference**: See [REFERENCE.md](references/REFERENCE.md) for all methods
- **Examples**: See [EXAMPLES.md](references/EXAMPLES.md) for common patterns
```

Claude loads FORMS.md, REFERENCE.md, EXAMPLES.md only when needed.

---

## Pattern 2: Domain-specific Organization

**When to Use**: When a large domain has distinct sub-domains

**Example Structure** (Skill with multiple domains):
```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── references/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

When a user asks about sales metrics, Claude reads only sales.md.

**Example Structure** (Skill supporting multiple frameworks/variations):
```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md (AWS deployment patterns)
    ├── gcp.md (GCP deployment patterns)
    └── azure.md (Azure deployment patterns)
```

When a user selects AWS, Claude reads only aws.md.

---

## Pattern 3: Conditional Details

**When to Use**: When advanced features are occasionally needed

**Example Structure**:
```markdown
# DOCX Processing

## Creating documents
Use docx-js for new documents. See [DOCX-JS.md](references/DOCX-JS.md).

## Editing documents
For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](references/REDLINING.md)
**For OOXML details**: See [OOXML.md](references/OOXML.md)
```

Claude reads the relevant reference only when the user needs that feature.

---

## Important Guidelines

1. **Avoid deep nesting**: References should maintain 1-level depth from SKILL.md. All reference files should be directly linked from SKILL.md.

2. **Structure long reference files**: Files over 100 lines should include a table of contents at the top so Claude can see the full scope when previewing.

3. **Avoid duplication**: Information should be in either SKILL.md or references files, not both.

## Practical Examples

### PDF Editor Skill

```
pdf-editor/
├── SKILL.md (Quick start + navigation)
└── references/
    ├── FORMS.md (form filling guide)
    ├── REFERENCE.md (API reference)
    └── EXAMPLES.md (common patterns)
```

### BigQuery Skill

```
bigquery-skill/
├── SKILL.md (overview + domain navigation)
└── references/
    ├── finance.md
    ├── sales.md
    ├── product.md
    └── marketing.md
```
