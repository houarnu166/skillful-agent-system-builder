# Skillful Agent System Builder

> A 3-stage collaborative system that designs custom Agent+Skill systems through Sam (Requirements) → Jenny (Design) → Will (Review)

[🇰🇷 한국어 버전 (Korean Version)](./README(kor).md)

## What is this?

Skillful Agent System Builder helps you create **custom AI agent teams** without writing complex prompts from scratch.

**Problem it solves**: Building multi-agent systems requires designing detailed prompts, defining agent roles, and ensuring quality. This is time-consuming and error-prone.

**Solution**: A 3-stage collaborative system where specialized AI agents (Sam, Jenny, Will) help you design production-ready agent systems through conversation.

## Getting Started (5 minutes)

### Option 1: Antigravity
1. Open this repository in your Antigravity IDE.
2. Ensure you have the Antigravity agent active.
3. Say: "Create an agent system for [your use case]"
4. Follow Sam's questions, review Jenny's design, get Will's quality check.

### Option 2: Claude Code
1. Clone this repository.
2. Open in Claude Code.
3. Say: "Create an agent system for [your use case]"
4. Follow Sam's questions, review Jenny's design, get Will's quality check.

Your custom agent system is ready in `./outputs/`.

## Terminology

- **Orchestrator**: The central manager that directs the workflow between you and the specialized agents.
- **Subagent**: Specialized AI agents (Sam, Jenny, Will) that handle specific parts of the process (Requirements, Design, Review).
- **Skill**: Specific capabilities or knowledge bases that agents uses to perform their tasks.
- **Start Prompt**: The final output file that contains the complete instruction set to spin up your custom agent team.

## Quick Start

### Prerequisites

1. **Context Files**: Ensure these files are in the `./context/` directory (included in this repository):
   - `anthropic-skills-guide.md`
   - `anthropic-subagents-guide.md`
   - `example-skills-mcp-builder-SKILL.md`
   - `example-skills-skill-creator-SKILL.md`

2. **Claude Code**: Make sure you have Claude Code installed and can access the `.agents/` directory.

### File Structure

```
skillful-agent-system-builder/
├── README.md                            # Main system documentation
├── AGENTS.md                            # Orchestrator & Agent guide
├── .agents/                              # Agent definitions
│   ├── skillful-orchestrator.md         # Main orchestrator
│   ├── sam-analyst.md                   # Requirements analyst
│   ├── jenny-engineer.md                # Design engineer
│   ├── will-reviewer.md                 # Quality reviewer
│   └── skills/                          # Skill definitions
│       ├── agent-design/
│       │   └── SKILL.md
│       ├── skill-design/
│       │   └── SKILL.md
│       ├── quality-checklist/
│       │   └── SKILL.md
│       └── anthropic-reference/
│           └── SKILL.md
├── context/                             # Persistent context & resources (always available)
├── inputs/                              # Task-specific input materials (temporary for current run)
├── temp/                                # Temporary session files
│   └── skillful-session/
└── outputs/                             # Final output generation path
```

## Usage

### Basic Commands

1. **Standard Request**: Full 3-stage process with questions
   ```
   "Create an agent system for educators"
   ```

2. **Fast Mode**: Skip questions, use reasonable assumptions
   ```
   "Proceed quickly. Design a content creation system for marketers"
   ```

3. **Stage-Specific Entry**:
   - `"Start from Sam"` - Start from requirements gathering
   - `"Go to Jenny stage"` - Start from detailed design (requires sam-draft.md)
   - `"Will review"` - Start from review (requires jenny-draft.md)

4. **Modification Requests**:
   ```
   "Sam, I think we need more Agents"
   "Jenny, please write more detailed Skill examples"
   "Will, please review again"
   ```

### Workflow

```
User Request
    ↓
[Orchestrator] Initialize session → Clear temp/ → Create session.json
    ↓
[Orchestrator] Recognize command → Determine entry point
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Stage 1: Requirements (Sam)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[sam-analyst] Gather requirements
    ├─ Fast mode? Skip questions
    └─ Normal mode? Ask 2-3 questions
    ↓
Output: ./temp/skillful-session/sam-draft.md
    ↓
[Orchestrator] User confirmation (if not fast mode)
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Stage 2: Design (Jenny)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[jenny-engineer] Create detailed design
    ├─ Design Agents (frontmatter + system prompt)
    ├─ Design Skills (SKILL.md structure)
    └─ Reference ./context/ as needed
    ↓
Output: ./temp/skillful-session/jenny-draft.md
    ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Stage 3: Review (Will)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ↓
[will-reviewer] Quality review
    ├─ Apply quality checklist
    ├─ Verify against ./context/ docs
    └─ Fix issues automatically
    ↓
Output: ./outputs/{system-name}-start-prompt.md
    ↓
🎉 Complete!
```

## System Components

### Agents

| Agent | Role | Key Responsibilities |
|-------|------|---------------------|
| **skillful-orchestrator** | Coordinator | Session management, command recognition, agent delegation |
| **sam-analyst** | Requirements Analyst | User interviews, requirement gathering, draft creation |
| **jenny-engineer** | Design Engineer | Agent/Skill detailed design, architecture decisions |
| **will-reviewer** | Quality Reviewer | Quality assurance, validation, final output generation |

### Skills

| Skill | Purpose | Used By |
|-------|---------|---------|
| **agent-design** | Agent frontmatter & system prompt guidelines | Jenny, Will |
| **skill-design** | Skill SKILL.md & bundled resources guidelines | Jenny, Will |
| **quality-checklist** | Comprehensive quality verification checklist | Will |
| **anthropic-reference** | Quick reference to ./context/ documentation | Jenny, Will |

## Output

The system generates a complete Start Prompt file in `./outputs/` with:

1. System overview
2. File structure
3. Complete Agent definitions (ready to copy to `.agents/`)
4. Complete Skill definitions (ready to copy to `.agents/skills/`)
5. Workflow documentation
6. Usage instructions
7. Example scenarios
8. Design decisions
9. Quality verification checklist

## Design Principles

1. **3-Stage Collaboration**: Separation of concerns (requirements → design → review)
2. **Progressive Disclosure**: Skills use 3-level loading (metadata → instructions → resources)
3. **Minimum Privilege**: Agents only get tools they need
4. **Session Isolation**: Clean temp/ on each new request
5. **Orchestrator Pattern**: All communication goes through orchestrator
6. **Quality First**: Comprehensive checklist validation

## Customization

### Add New Agent

Create a new `.md` file in `.agents/` with proper frontmatter and system prompt.

### Add New Skill

1. Create directory: `.agents/skills/{skill-name}/`
2. Create `SKILL.md` with frontmatter and content
3. Add optional: `references/`, `scripts/`, `assets/`

### Modify Workflow

Edit the orchestrator's workflow phases in [skillful-orchestrator.md](.agents/skillful-orchestrator.md).

## Troubleshooting

### Session Issues
- **Problem**: Old session data interfering
- **Solution**: Orchestrator auto-clears `./temp/skillful-session/` on each new request

### Missing Context Files
- **Problem**: Jenny/Will can't validate against Anthropic docs
- **Solution**: Ensure all 4 files are in `./context/` directory

### Permission Errors
- **Problem**: Agents can't write files
- **Solution**: Check `permissionMode: acceptEdits` in frontmatter

## Examples

### Example 1: Education System
```
User: "Create an agent system for educators"

Output: ./outputs/educator-system-start-prompt.md
- 4 Agents: orchestrator, lesson-planner, exam-creator, parent-communicator
- 3 Skills: education-curriculum, assessment-design, communication-templates
```

### Example 2: Code Review System
```
User: "Proceed quickly. I need a code review automation system"

Output: ./outputs/code-review-automation-start-prompt.md
- 3 Agents: orchestrator, code-analyzer, security-checker
- 2 Skills: code-quality-rules, security-best-practices
```

## Contributing

To improve this system:

1. Add more example Skills in `.agents/skills/`
2. Enhance quality checklists in `quality-checklist/SKILL.md`
3. Add more context documentation in `./context/`
4. Improve error handling in orchestrator

## Resources

- [Anthropic Skills Guide](./context/anthropic-skills-guide.md)
- [Anthropic Subagents Guide](./context/anthropic-subagents-guide.md)
- [Example: MCP Builder](./context/example-skills-mcp-builder-SKILL.md)
- [Example: Skill Creator](./context/example-skills-skill-creator-SKILL.md)

---

**Version**: 1.0
**Created**: 2026-01-17
**Status**: ✅ Production Ready
**author**: [Yz](mailto:houarnu166@gmail.com)

**contributor**: 해당 시스템은 [필로소피 AI](https://www.youtube.com/@%ED%95%84%EB%A1%9C%EC%86%8C%ED%94%BC)의 유튜브 영상과 함께 제공된 프롬프트와 시스템에 영향을 받아 제작됐습니다.    This system was developed based on the prompts and system provided alongside the YouTube video from [Philosophy AI](https://www.youtube.com/@%ED%95%84%EB%A1%9C%EC%86%8C%ED%94%BC).
