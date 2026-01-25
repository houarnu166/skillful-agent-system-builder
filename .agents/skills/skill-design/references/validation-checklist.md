# Skill Validation Checklist

Verify the following items after completing Skill development.

## Naming Convention Validation

```bash
# name validation
if [[ "$name" =~ ^[a-z0-9-]+$ ]] && [ ${#name} -le 64 ]; then
  echo "✓ Valid name"
fi

# description length validation
if [ ${#description} -le 1024 ]; then
  echo "✓ Description length OK"
fi
```

---

## Frontmatter Checklist

- [ ] `name`: lowercase + hyphens only
- [ ] `name`: 1-64 characters
- [ ] `name`: matches directory name
- [ ] `name`: no reserved words (anthropic, claude)
- [ ] `name`: no hyphen at start/end
- [ ] `name`: no consecutive hyphens
- [ ] `description`: 1-1024 characters
- [ ] `description`: includes Skill function description
- [ ] `description`: includes trigger conditions ("Use when...")
- [ ] `description`: lists usage scenarios

---

## Structure Checklist

- [ ] SKILL.md under 500 lines
- [ ] Progressive disclosure pattern applied
- [ ] References are 1-level depth only
- [ ] No unnecessary files:
  - ❌ README.md
  - ❌ INSTALLATION_GUIDE.md
  - ❌ QUICK_REFERENCE.md
  - ❌ CHANGELOG.md

---

## Content Checklist

- [ ] Using concise examples (no verbose explanations)
- [ ] Degrees of Freedom considered
- [ ] No duplicate information (SKILL.md vs references)
- [ ] Clear usage instructions
- [ ] No "When to Use" section in Body (should be in description)

---

## Resources Checklist

- [ ] Scripts testing completed
- [ ] References properly separated
- [ ] Assets include only what is needed
- [ ] File reference paths are correct

---

## Common Mistakes Check

- [ ] ❌ Did you not omit trigger conditions in description?
- [ ] ❌ Did you not include "When to Use This Skill" section in Body?
- [ ] ❌ Does SKILL.md not exceed 500 lines?
- [ ] ❌ Is there no 2+ level nesting in references?
- [ ] ❌ Is there no duplicate information between SKILL.md and references?
- [ ] ❌ Did you not explain information Claude already knows?

---

## Final Check Before Packaging

```bash
# Run packaging script (if available)
scripts/package_skill.py <path/to/skill-folder>
```

Items the packaging script validates:
1. YAML frontmatter format
2. Required fields exist
3. Naming conventions
4. Directory structure
5. Description quality
6. File composition
