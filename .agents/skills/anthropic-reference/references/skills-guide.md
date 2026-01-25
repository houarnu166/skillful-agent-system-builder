# Skills Official Guide Essentials

## Skill Definition

A Skill is a directory containing at minimum a `SKILL.md` file.

```
skill-name/
├── SKILL.md          # Required
├── scripts/          # Optional
├── references/       # Optional
└── assets/           # Optional
```

---

## Frontmatter Fields

| Field | Required | Constraints |
|-----|-----|------|
| `name` | Yes | Max 64 characters. Lowercase, numbers, hyphens only. |
| `description` | Yes | Max 1024 characters. Describes Skill function and when to use. |
| `license` | No | License name or bundled file reference |
| `compatibility` | No | Max 500 characters. Environment requirements |
| `metadata` | No | Key-value metadata mapping |
| `allowed-tools` | No | Space-separated pre-approved tool list (experimental) |

---

## name Field Rules

- 1-64 characters
- Lowercase alphanumeric + hyphens only (`a-z`, `0-9`, `-`)
- No hyphen at start/end
- No consecutive hyphens (`--`)
- Must match parent directory name

**Valid**:
```yaml
name: pdf-processing
name: data-analysis
name: code-review
```

**Invalid**:
```yaml
name: PDF-Processing  # Uppercase not allowed
name: -pdf            # Starting hyphen
name: pdf--editor     # Consecutive hyphens
```

---

## description Field Rules

- 1-1024 characters
- Describes both Skill function and when to use
- Include keywords helpful for search

**Good Example**:
```yaml
description: |
  Extract text and tables from PDF files, fill forms, merge documents.
  Use when working with PDF documents for: (1) text extraction,
  (2) form filling, (3) document merging.
```

---

## Progressive Disclosure (3-Level Loading)

| Level | Loading Time | Recommended Size |
|-----|---------|---------|
| Metadata | At start for all skills | ~100 tokens |
| Instructions | When Skill is activated | < 5000 tokens |
| Resources | When needed | Unlimited |

**Guidelines**:
- Keep SKILL.md under 500 lines
- Separate detailed references into separate files
- File references should be 1-level depth from SKILL.md

---

## Bundled Resources

### scripts/
- Executable code (Python, Bash, JavaScript, etc.)
- Self-contained or dependencies documented
- Include useful error messages
- Handle edge cases

### references/
- Detailed technical references
- Form templates/structured data
- Domain-specific files (`finance.md`, `legal.md`, etc.)

### assets/
- Templates (documents, configurations)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)

---

## Validation Checklist

- [ ] name: lowercase + hyphens, 1-64 characters
- [ ] name: matches directory name
- [ ] description: 1-1024 characters
- [ ] description: includes function + when to use
- [ ] SKILL.md: under 500 lines
- [ ] References: 1-level depth only
