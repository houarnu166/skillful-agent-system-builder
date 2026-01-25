# Agent Validation Details

## Frontmatter Validation

### name Field
| Rule | Validation |
|-----|------|
| Format | lowercase + hyphens only |
| Length | 1-64 characters |
| Start/End | hyphen not allowed |
| Consecutive | consecutive hyphens not allowed |
| Reserved words | "anthropic", "claude" not allowed |

**Validation Command**:
```bash
[[ "$name" =~ ^[a-z][a-z0-9-]*[a-z0-9]$ ]] && [ ${#name} -le 64 ]
```

### description Field
| Rule | Validation |
|-----|------|
| Length | 1-1024 characters |
| Role description | must be included |
| Trigger conditions | "Use when...", "Trigger: ...", etc. |

**Check Items**:
- [ ] Is the role clearly defined?
- [ ] Is it specified when to use?
- [ ] Can Claude auto-delegate?

### tools Field
**Minimum Privilege Principle Validation**:
| Agent Type | Allowed Tools | Forbidden Tools |
|-----------|----------|----------|
| Read-only | Read, Grep, Glob, Bash | Write, Edit |
| Writer | Read, Write, Edit, Bash | Task |
| Orchestrator | Read, Write, Task | - |
| Worker | Only what is needed for the role | Task |

### model Field
| Choice | Suitable When |
|-----|------------|
| haiku | Simple exploration, fast response needed |
| sonnet | General tasks, default |
| opus | Complex reasoning, high accuracy needed |
| inherit | Same as parent |

### permissionMode Field
| Mode | Suitable When | Caution |
|-----|------------|---------|
| default | General situations | - |
| acceptEdits | Automated build/deploy | Only for trusted tasks |
| plan | Planning phase | Read-only |
| bypassPermissions | Test environment | **Not for production** |

---

## System Prompt Validation

### Required Sections
- [ ] Role declaration: "You are {name}, a {expert type}."
- [ ] Role description: "## Your Role"
- [ ] Process: "## Process" with steps
- [ ] Output format: "## Output Format"

### Recommended Sections
- [ ] Invocation conditions: "## When Called"
- [ ] Notes: "## Important Notes"
- [ ] Examples: Input/output examples

### Quality Criteria
| Criteria | Validation |
|-----|------|
| Clarity | Can the role be explained in one sentence? |
| Specificity | Are the process steps actionable? |
| Completeness | Is the output format defined? |
