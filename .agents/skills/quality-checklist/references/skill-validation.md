# Skill Validation Details

## Frontmatter Validation

### name Field
| Rule | Validation |
|-----|------|
| Format | lowercase + hyphens only |
| Length | 1-64 characters |
| Directory | must match directory name |
| Reserved words | "anthropic", "claude" not allowed |

**Validation Command**:
```bash
skill_dir=$(basename "$PWD")
[[ "$name" == "$skill_dir" ]]
```

### description Field
| Rule | Validation |
|-----|------|
| Length | 1-1024 characters |
| Function description | What the Skill does |
| Trigger conditions | "Use when...", "Triggers: ..." |
| Scenarios | (1), (2), (3)... format recommended |

**Good Example**:
```yaml
description: |
  Guide for designing SKILL.md files with bundled resources.
  Use when: (1) Creating new Skills, (2) Reviewing Skill quality,
  (3) Refactoring existing Skills.
  Triggers: "create skill", "skill design", "SKILL.md"
```

---

## Body Content Validation

### Structure Checklist
- [ ] Overview section exists
- [ ] Core concepts explained
- [ ] Usage method detailed
- [ ] Examples are concrete
- [ ] Notes specified

### Length Validation
| Criteria | Recommended | Warning |
|-----|------|------|
| SKILL.md | < 500 lines | > 500 lines → separate to references |
| Tokens | < 5k tokens | > 5k → progressive disclosure |

**Validation Command**:
```bash
wc -l SKILL.md  # < 500 recommended
```

---

## Bundled Resources Validation

### scripts/
- [ ] Executable (`chmod +x`)
- [ ] Error handling included
- [ ] Dependencies documented
- [ ] Testing completed

### references/
- [ ] 1-level depth only
- [ ] Direct link from SKILL.md
- [ ] No duplicate content
- [ ] Large files include table of contents

### assets/
- [ ] Only files used for output
- [ ] No need to load into context
- [ ] Templates, images, etc.

---

## Progressive Disclosure Validation

### 3-Level Loading
| Level | Validation |
|-------|------|
| Metadata | name + description < 100 words |
| Instructions | SKILL.md body < 5k tokens |
| Resources | Load on demand structure |

### Pattern Application Check
- [ ] Multiple features → Pattern 1 (references links)
- [ ] Sub-domains → Pattern 2 (domain-specific files)
- [ ] Advanced features → Pattern 3 (conditional loading)
