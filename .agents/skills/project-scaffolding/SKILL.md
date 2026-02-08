---
name: project-scaffolding
description: Instructions for parsing Start Prompts and generating the standard Skillful Agent System directory structure.
---

# Project Scaffolding Skill

This skill guides you through parsing a "Start Prompt" markdown file and creating the corresponding file structure.

## 1. Directory Structure Standard

You must create the following structure structure in the target directory:

```text
{system-name}/
├── .agents/
│   ├── {agent1-name}.md
│   ├── {agent2-name}.md
│   └── skills/
│       └── {skill-name}/
│           └── SKILL.md
└── AGENTS.md (or specified generic doc)
```

## 2. Parsing Strategy

The Start Prompt file usually contains sections for Agents and Skills.

### Extracting Agents
Look for sections describing Agents. They typically contain:
*   Name
*   Description / Role
*   Frontmatter (YAML)
*   System Prompt / Instructions

**Action**: Create `.agents/{name}.md` with the extracted content. Ensure the YAML frontmatter is at the very top.

### Extracting Skills
Look for sections describing Skills.
*   Name
*   Description
*   Instructions (Markdown content)

**Action**:
1.  Create directory `.agents/skills/{skill-name}/`
2.  Create `SKILL.md` inside it.
3.  Add YAML frontmatter:
    ```yaml
    ---
    name: {skill-name}
    description: {short-description}
    ---
    ```
4.  Add the skill instructions below the frontmatter.

## 3. Execution Steps

1.  **Initialize**: `mkdir -p {target_dir}/.agents/skills`
2.  **Generate Agents**: loop through identified agents and write files.
3.  **Generate Skills**: loop through identified skills and write files.
4.  **Finalize**: Write a basic `AGENTS.md` referencing the created agents.

## 4. Safety Checks

*   **Sanitize Paths**: Ensure filenames do not contain special characters or spaces (use hyphens).
*   **Overwrite Protection**: If the directory is not empty, warn or append a timestamp (though Orchestrator usually handles this).
