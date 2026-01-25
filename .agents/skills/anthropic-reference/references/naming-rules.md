# Naming Rules Summary

## Common name Field Rules

Same rules apply to both Skills and Agents:

| Rule | Details |
|-----|------|
| Length | 1-64 characters |
| Allowed characters | Lowercase (`a-z`), numbers (`0-9`), hyphens (`-`) |
| Start/End | Hyphens not allowed |
| Consecutive | Consecutive hyphens (`--`) not allowed |
| Reserved words | `anthropic`, `claude` not allowed |

---

## Additional Skill name Rules

- **Directory match**: name must match parent directory name

```
skill-name/           # Directory
└── SKILL.md
    ---
    name: skill-name  # Must match
    ---
```

---

## Valid Examples

```yaml
# Skills
name: pdf-processing
name: data-analysis
name: code-review
name: brand-guidelines
name: mcp-builder

# Agents
name: code-reviewer
name: test-runner
name: sam-analyst
name: jenny-engineer
```

---

## Invalid Examples

```yaml
# Uppercase not allowed
name: PDF-Processing  # ❌
name: DataAnalysis    # ❌

# Hyphen rule violations
name: -pdf            # ❌ Starting hyphen
name: pdf-            # ❌ Ending hyphen
name: pdf--editor     # ❌ Consecutive hyphens

# Disallowed characters
name: pdf_processing  # ❌ Underscore
name: pdf.processing  # ❌ Period
name: pdf processing  # ❌ Space

# Reserved words
name: anthropic-tool  # ❌ Contains reserved word
name: claude-helper   # ❌ Contains reserved word
```

---

## description Field Rules

| Item | Skill | Agent |
|-----|-------|-------|
| Length | 1-1024 characters | 1-1024 characters |
| Required content | Function + when to use | Role + trigger conditions |
| Keywords | Include searchable keywords | Claude auto-delegation trigger |

---

## Validation Regex

```bash
# name validation
[[ "$name" =~ ^[a-z][a-z0-9-]*[a-z0-9]$ ]] && \
[[ ! "$name" =~ -- ]] && \
[ ${#name} -le 64 ] && \
[[ ! "$name" =~ anthropic|claude ]]
```

---

## Quick Check

| Check | Method |
|-----|------|
| Lowercase only? | `echo "$name" \| grep -q '^[a-z0-9-]*$'` |
| 64 chars or less? | `[ ${#name} -le 64 ]` |
| Hyphen rules? | `[[ ! "$name" =~ ^-\|-$\|-- ]]` |
| No reserved words? | `[[ ! "$name" =~ anthropic\|claude ]]` |
| Directory match? (Skill) | `[ "$name" == "$(basename $PWD)" ]` |
