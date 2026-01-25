# Bundled Resources Guide

Skills can optionally include bundled resources in addition to SKILL.md.

## Directory Structure

```
skill-name/
├── SKILL.md (required)
├── scripts/          - Executable code (Python/Bash/etc.)
├── references/       - Documents loaded into context when needed
└── assets/           - Files used for output (templates, icons, fonts, etc.)
```

## scripts/ (Executable Code)

**Purpose**: Tasks requiring deterministic reliability or repetitively rewritten

**When to Use**:
- When the same code is repeatedly rewritten
- When deterministic reliability is needed
- When complex data processing is required

**Examples**: `scripts/rotate_pdf.py`, `scripts/validate_schema.py`

**Advantages**:
- Token efficient
- Deterministic
- Can be executed without loading into context

**Structure Example**:
```
.agents/skills/{skill-name}/
└── scripts/
    ├── process.py
    └── validate.sh
```

## references/ (Reference Documents)

**Purpose**: Documents Claude needs to reference while working

**When to Use**:
- When detailed documentation is needed
- When domain-specific separation is needed
- When a collection of examples is needed

**Examples**: `references/finance.md`, `references/api_docs.md`

**Best practice**:
- For large files (>10k words), include grep search patterns in SKILL.md
- **Avoid duplication**: Information should only be in either SKILL.md or references file
- **1-level depth only**: All reference files should be directly linked from SKILL.md

**Structure Example**:
```
.agents/skills/{skill-name}/
└── references/
    ├── REFERENCE.md
    ├── EXAMPLES.md
    └── domain-specific.md
```

## assets/ (Output Files)

**Purpose**: Files used for output without being loaded into context

**When to Use**:
- When Skill needs files for final output
- When template files are needed
- When resources like images/fonts are needed

**Examples**: `assets/logo.png`, `assets/slides.pptx`, `assets/frontend-template/`

**Structure Example**:
```
.agents/skills/{skill-name}/
└── assets/
    ├── template.html
    └── logo.png
```

## Degrees of Freedom Framework

Adjust the level of specificity to match the fragility and variability of the task:

### High Freedom (Text-based Instructions)
- When multiple approaches are valid
- When decisions depend on context

### Medium Freedom (Pseudocode or Scripts with Parameters)
- When preferred patterns exist
- When some variation is allowed

### Low Freedom (Specific Scripts, Few Parameters)
- When tasks are fragile and error-prone
- When consistency is important

**Analogy**: Think of Claude as navigating a path. A narrow bridge over a cliff requires specific guardrails (low freedom), but an open field allows many routes (high freedom).

## How to Reference Files

Referencing other files from SKILL.md:

```markdown
See [detailed reference](references/REFERENCE.md) for more info.

Run the script:
```bash
python scripts/process.py
```

**Principle**: Maintain 1-level depth only (avoid deep nesting)
