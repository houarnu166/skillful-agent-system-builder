# Tool Selection Guide

## Minimum Privilege Principle

Grant only the necessary tools to Agents. Excessive permissions cause security risks and unexpected behavior.

---

## Recommended Tools by Role

### Read-only Agent (Exploration/Analysis)
```yaml
tools: [Read, Grep, Glob, Bash]
```
- Code analysis, search, exploration
- Cannot modify files

### Writer Agent (File Modification)
```yaml
tools: [Read, Write, Edit, Bash]
```
- Code writing, file creation/modification
- Add Grep, Glob if needed

### Interactive Agent (User Interaction)
```yaml
tools: [Read, AskUserQuestion]
```
- Requirements gathering, interviews
- Clarification questions

### Orchestrator Agent (Coordinator)
```yaml
tools: [Read, Write, Task, Bash]
```
- Subagent invocation and coordination
- `Task` tool required

---

## Tool Combination Patterns

| Pattern | Tools | Use Case |
|-----|------|------|
| Explorer | `Read, Grep, Glob` | Codebase exploration |
| Builder | `Read, Write, Edit, Bash` | Implementation work |
| Reviewer | `Read, Grep, Glob` | Code review |
| Interviewer | `Read, AskUserQuestion` | Requirements gathering |
| Orchestrator | `Read, Write, Task` | Workflow coordination |

---

## Cautions

### Bash Tool
- Can execute commands → grant carefully
- Often needed for read-only work too (git, npm, etc.)

### Task Tool
- For Subagent invocation only
- Grant only to Orchestrator
- Do not grant to Worker Agents

### Write vs Edit
- `Write`: Overwrites entire file
- `Edit`: Partial modification (sed-style)
- Most cases need both

---

## Combination with PermissionMode

| Situation | permissionMode | tools |
|-----|---------------|-------|
| Automated build | `acceptEdits` | `Read, Write, Edit, Bash` |
| Planning phase | `plan` | `Read, Grep, Glob` |
| Risky operations | `default` | Minimum only |
