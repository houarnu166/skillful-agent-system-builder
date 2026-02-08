# Stage Execution & Protocols

Module Context: Detailed execution protocols for the 4-stage workflow. Referenced by root AGENTS.md.

## Stage 1: Sam (Requirements Analysis)

```
Invoke: sam-analyst
Input:
  - user_request: Original user message
  - fast_mode: true/false from Phase 1
Output: ./temp/skillful-session/sam-draft.md
Confirmation: If fast_mode=false, show draft and request approval
State Update: current_stage = "sam_complete"
```

**Pre-check**: None (first stage)

**Verification**:
```bash
test -f ./temp/skillful-session/sam-draft.md || echo "Error: sam-draft.md missing"
```

## Stage 2: Jenny (Detailed Design)

```
Invoke: jenny-engineer
Input: Read ./temp/skillful-session/sam-draft.md
Context: ./context/anthropic-*.md
Skills: agent-design, skill-design, anthropic-reference
Output: ./temp/skillful-session/jenny-draft.md
Confirmation: Required (unless fast_mode=true)
State Update: current_stage = "jenny_complete"
```

**Pre-check**: sam-draft.md must exist

**Verification**:
```bash
test -f ./temp/skillful-session/jenny-draft.md || echo "Error: jenny-draft.md missing"
```

## Stage 3: Will (Quality Review)

```
Invoke: will-reviewer
Input: Read ./temp/skillful-session/jenny-draft-approved.md
Context: ./context/anthropic-*.md (validation)
Skills: quality-checklist, agent-design, skill-design

Process:
1. Validate jenny-draft-approved.md integrity (check hash)
2. Classify issues: Category 1 (auto-fix) / 2 (design) / 3 (requirement)
3. Apply Category 1 fixes -> will-fixes.md
4. If Category 2: Generate will-feedback.md, ask user decision
5. If Category 3: AskUserQuestion
6. Output: ./temp/skillful-session/will-approved.json

State Update: current_stage = "will_complete"
```

**Pre-check**: jenny-draft.md + user approval (unless fast_mode)

## Stage 4: Tom (Output Generation)

```
Invoke: tom-builder
Input: ./temp/skillful-session/will-approved.json
Skills: project-scaffolding, agent-design, skill-design
Output: 
  - ./outputs/{system-name}-start-prompt.md
  - ./outputs/{system-name}/ (project files)
State Update: current_stage = "complete"
```

---

## Jenny Review Protocol

**Trigger**: After Jenny completes jenny-draft.md (unless fast_mode=true)

**Process**:

1. Generate Summary (jenny-summary.md):
   - System Overview: name, purpose, agent/skill counts
   - Agent Structure: role, tools, skills per agent
   - Skill Definitions: purpose, trigger pattern, tools
   - Key Design Decisions: rationale for architectural choices
   - User Review Points: checklist for approval

2. Display to User:
   - Keep under 50 lines
   - Emphasize action commands

3. Handle Response:

| Response | Action |
|----------|--------|
| "approve", "ok", "yes" | Lock design, proceed to Will |
| "Jenny, [request]" | Re-invoke jenny with modification |
| "show full" | Display full jenny-draft.md |
| "restart" | Clear session, restart from Sam |

4. On Approval:
   - Create locked copy: jenny-draft-approved.md (chmod 444)
   - Generate integrity hash
   - Update session.json: current_stage = "jenny_approved"
   - Proceed to Will immediately

---

## Will-Jenny Feedback Loop

**Purpose**: Enable Will to request redesign for structural issues while preventing infinite loops.

### 3-Category Issue Classification

| Category | Examples | Action |
|----------|----------|--------|
| 1: Auto-fixable | Typos, naming, YAML format | Will fixes immediately |
| 2: Design Issues | Role overlap, permission mismatch | Generate feedback, ask user |
| 3: Requirement Issues | Missing functionality, scope | AskUserQuestion |

### Loop Control

```json
{
  "will_jenny_loop": {
    "max_iterations": 3,
    "current_iteration": 0
  }
}
```

**Enforcement**:
- After 3 iterations: Stop loop, escalate to user
- Options: Use v1 (approved) / Use latest / Restart from Sam

### Structured Feedback Format

```yaml
# will-feedback.md
---
format_version: 1.0
revision_scope: SURGICAL
max_changes: 1
---

## Issue: [Description]
**Severity**: high/medium/low
**Confidence**: 0.0-1.0

**Scope**:
  agents: ["affected-agent"]
  skills: []
  workflow: false

**Current State**:
  [field and value]

**Required Change**:
  [new value]

**Constraints**:
- DO NOT modify other agents
- ONLY update specified field
```

---

## User Decision Points

### After Category 2 Issues Found:

```
Will found design issues

Issue: [Description]
Severity: High | Confidence: 85%
Problem: [explanation]
Impact: [consequences]
Suggestion: [recommended fix]

Options:
1. Ignore and proceed (faster)
2. Request Jenny revision (improves quality)
3. View details (see will-feedback.md)
```

### After Max Iterations Reached:

```
Design improvement limit reached (3 iterations)

Available Versions:
- v1: User-approved original
- v3: Latest revision

Options:
1. Use v1 (recommended)
2. Use v3
3. Restart from Sam
4. Manual intervention
```

---

## Fast Mode Behavior

When fast_mode=true:
- Skip Sam questions, immediate draft
- Skip Jenny review (no user approval)
- Will auto-fixes all categories
- No will-feedback.md generated
- No Will-Jenny loop
- Direct to Tom output

---

## Session State Schema

```json
{
  "session_id": "timestamp",
  "started_at": "ISO-datetime",
  "current_stage": "init|sam_complete|jenny_complete|jenny_approved|will_complete|complete",
  "fast_mode": false,
  "versions": {
    "jenny_draft": {
      "approved": "jenny-draft-approved.md",
      "approved_hash": "SHA256",
      "current": "jenny-draft-revised.md"
    }
  },
  "will_jenny_loop": {
    "max_iterations": 3,
    "current_iteration": 0,
    "user_decisions": []
  }
}
```

---

## Temporary File Convention

| File | Purpose |
|------|---------|
| sam-draft.md | Sam's requirements output |
| jenny-draft.md | Jenny's design output |
| jenny-summary.md | User review summary |
| jenny-draft-approved.md | Locked approved design (read-only) |
| jenny-approved.hash | SHA256 integrity hash |
| will-feedback.md | Category 2 issues (if found) |
| will-fixes.md | Category 1 auto-fixes log |
| jenny-draft-revised.md | After Jenny responds to feedback |
| will-approved.json | Final validation result |

---

## Structural Consistency Validation

Before accepting Jenny revision:
1. Compare agent count (must match approved)
2. Compare skill count (must match approved)
3. Compare workflow type (must match approved)

If violations found: Alert user with diff, request approval for structural changes.

---

## Subagent Skills Reference

| Subagent | Skills |
|----------|--------|
| Sam | agent-design-basics, concise-planning |
| Jenny | agent-design, skill-design, anthropic-reference, autonomous-agent-patterns, agent-memory-systems, prompt-engineering, agent-tool-builder, context-window-management, multi-agent-brainstorming |
| Will | quality-checklist, agent-design, skill-design, prompt-engineering, agent-evaluation, verification-before-completion, context-window-management |
| Tom | project-scaffolding, documentation-templates, agent-design, skill-design |
