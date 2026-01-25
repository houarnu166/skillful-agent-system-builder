# Common Issues and Solutions

## Agent-Related Issues

### Issue 1: Missing Trigger Conditions in description
**Symptom**: Claude cannot auto-delegate to the Agent

**Bad Example**:
```yaml
description: Code reviewer for quality checks
```

**Good Example**:
```yaml
description: |
  Code reviewer for quality checks.
  Trigger: Use proactively after code changes or when user requests review.
```

---

### Issue 2: Excessive Permission Grant
**Symptom**: Worker Agent includes unnecessary tools

**Bad Example**:
```yaml
tools: [Read, Write, Edit, Bash, Task, AskUserQuestion]  # All tools
```

**Good Example**:
```yaml
tools: [Read, Grep, Glob]  # Read-only Agent uses only read tools
```

---

### Issue 3: Task Tool in Worker Agent
**Symptom**: Worker attempts to call other Subagents

**Problem**:
```yaml
# Worker Agent
tools: [Read, Write, Task]  # ❌ Task is for Orchestrator only
```

**Solution**:
```yaml
# Worker Agent
tools: [Read, Write]  # ✅ Task removed
```

---

## Skill-Related Issues

### Issue 4: SKILL.md Exceeds 500 Lines
**Symptom**: Context window overload

**Solution**: Separate to references/
```
skill/
├── SKILL.md (< 500 lines, core only)
└── references/
    ├── detailed-guide.md
    └── examples.md
```

---

### Issue 5: "When to Use" Section in Body
**Symptom**: Trigger information is in the body instead of description

**Problem**: Body is loaded after trigger → Claude cannot reference at trigger time

**Solution**: Include all trigger information in description

---

### Issue 6: References Nested More Than 2 Levels
**Symptom**: Another directory inside references/

**Bad Example**:
```
references/
└── advanced/
    └── detailed/
        └── guide.md  # ❌ 3-level nesting
```

**Good Example**:
```
references/
├── guide.md
└── advanced-guide.md  # ✅ 1-level
```

---

## Structure-Related Issues

### Issue 7: Direct Calls Between Subagents
**Symptom**: Worker A attempts to directly call Worker B

**Rule**: All Subagent calls must go through Orchestrator

```
✅ User → Orchestrator → Worker A
                      → Worker B

❌ User → Worker A → Worker B
```

---

### Issue 8: Distributed State Management
**Symptom**: Each Worker saves state independently

**Solution**: Orchestrator manages central state
```
temp/session/
├── session.json      # Managed by Orchestrator
├── worker-a-output.md
└── worker-b-output.md
```

---

## Validation Commands

### Full Structure Check
```bash
# Check Agent files
ls .agents/*.md

# Check Skill structure
find .agents/skills -name "SKILL.md"

# Check References depth
find .agents/skills -type d -depth 2
```

### Naming Convention Check
```bash
# Extract and verify name field
grep "^name:" .agents/*.md .agents/skills/*/SKILL.md
```
