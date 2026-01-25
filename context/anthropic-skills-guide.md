# Overview

> A simple, open format for giving agents new capabilities and expertise.
> 

Agent Skills are folders of instructions, scripts, and resources that agents can discover and use to do things more accurately and efficiently.

## Why Agent Skills?

Agents are increasingly capable, but often don't have the context they need to do real work reliably. Skills solve this by giving agents access to procedural knowledge and company-, team-, and user-specific context they can load on demand. Agents with access to a set of skills can extend their capabilities based on the task they're working on.

**For skill authors**: Build capabilities once and deploy them across multiple agent products.

**For compatible agents**: Support for skills lets end users give agents new capabilities out of the box.

**For teams and enterprises**: Capture organizational knowledge in portable, version-controlled packages.

## What can Agent Skills enable?

- **Domain expertise**: Package specialized knowledge into reusable instructions, from legal review processes to data analysis pipelines.
- **New capabilities**: Give agents new capabilities (e.g. creating presentations, building MCP servers, analyzing datasets).
- **Repeatable workflows**: Turn multi-step tasks into consistent and auditable workflows.
- **Interoperability**: Reuse the same skill across different skills-compatible agent products.

## Adoption

Agent Skills are supported by leading AI development tools.

## Open development

The Agent Skills format was originally developed by [Anthropic](https://www.anthropic.com/), released as an open standard, and has been adopted by a growing number of agent products. The standard is open to contributions from the broader ecosystem.

[View on GitHub](https://github.com/agentskills/agentskills)

---

> To find navigation and other pages in this documentation, fetch the llms.txt file at: https://agentskills.io/llms.txt
> 

# What are skills?

A skill is a directory containing at minimum a `SKILL.md` file:    The `SKILL.md` file must contain YAML frontmatter followed by Markdown content.

| Field | Required | Constraints |
| --- | --- | --- |
| `name` | Yes | Max 64 characters. Lowercase letters, numbers, and hyphens only. Must not start or end with a hyphen. |
| `description` | Yes | Max 1024 characters. Non-empty. Describes what the skill does and when to use it. |
| `license` | No | License name or reference to a bundled license file. |
| `compatibility` | No | Max 500 characters. Indicates environment requirements (intended product, system packages, network access, etc.). |
| `metadata` | No | Arbitrary key-value mapping for additional metadata. |
| `allowed-tools` | No | Space-delimited list of pre-approved tools the skill may use. (Experimental) |

The required `name` field:

- Must be 1-64 characters
- May only contain unicode lowercase alphanumeric characters and hyphens (`a-z` and )
- Must not start or end with
- Must not contain consecutive hyphens (`-`)
- Must match the parent directory name

The required `description` field:

- Must be 1-1024 characters
- Should describe both what the skill does and when to use it
- Should include specific keywords that help agents identify relevant tasks
- Specifies the license applied to the skill
- We recommend keeping it short (either the name of a license or the name of a bundled license file)
- Must be 1-500 characters if provided
- Should only be included if your skill has specific environment requirements
- Can indicate intended product, required system packages, network access needs, etc.
- A map from string keys to string values
- Clients can use this to store additional properties not defined by the Agent Skills spec
- We recommend making your key names reasonably unique to avoid accidental conflicts
- A space-delimited list of tools that are pre-approved to run
- Experimental. Support for this field may vary between agent implementations
- Step-by-step instructions
- Examples of inputs and outputs
- Common edge cases

Note that the agent will load this entire file once it’s decided to activate a skill. Consider splitting longer `SKILL.md` content into referenced files.

- Be self-contained or clearly document dependencies
- Include helpful error messages
- Handle edge cases gracefully

Supported languages depend on the agent implementation. Common options include Python, Bash, and JavaScript.

- `REFERENCE.md` - Detailed technical reference
- `FORMS.md` - Form templates or structured data formats
- Domain-specific files (`finance.md`, `legal.md`, etc.)

Keep individual [reference files](https://agentskills.io/specification/#file-references) focused. Agents load these on demand, so smaller files mean less use of context.

- Templates (document templates, configuration templates)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)
1. **Metadata** (~100 tokens): The `name` and `description` fields are loaded at startup for all skills
2. **Instructions** (< 5000 tokens recommended): The full `SKILL.md` body is loaded when the skill is activated
3. **Resources** (as needed): Files (e.g. those in `scripts/`, `references/`, or `assets/`) are loaded only when required

Keep your main `SKILL.md` under 500 lines. Move detailed reference material to separate files.    Keep file references one level deep from `SKILL.md`. Avoid deeply nested reference chains.    This checks that your `SKILL.md` frontmatter is valid and follows all naming conventions.

# Specification

> The complete format specification for Agent Skills.
> 

This document defines the Agent Skills format.

## Directory structure

A skill is a directory containing at minimum a `SKILL.md` file:

```
skill-name/
└── SKILL.md          # Required

```

<Tip>
You can optionally include [additional directories](https://www.notion.so/Agent-Skills-2ead1319e87a81cd87c1c7a4f8098665?pvs=21) such as `scripts/`, `references/`, and `assets/` to support your skill.
</Tip>

## [SKILL.md](http://skill.md/) format

The `SKILL.md` file must contain YAML frontmatter followed by Markdown content.

### Frontmatter (required)

```yaml
---
name: skill-name
description: A description of what this skill does and when to use it.
---

```

With optional fields:

```yaml
---
name: pdf-processing
description: Extract text and tables from PDF files, fill forms, merge documents.
license: Apache-2.0
metadata:
  author: example-org
  version: "1.0"
---

```

| Field | Required | Constraints |
| --- | --- | --- |
| `name` | Yes | Max 64 characters. Lowercase letters, numbers, and hyphens only. Must not start or end with a hyphen. |
| `description` | Yes | Max 1024 characters. Non-empty. Describes what the skill does and when to use it. |
| `license` | No | License name or reference to a bundled license file. |
| `compatibility` | No | Max 500 characters. Indicates environment requirements (intended product, system packages, network access, etc.). |
| `metadata` | No | Arbitrary key-value mapping for additional metadata. |
| `allowed-tools` | No | Space-delimited list of pre-approved tools the skill may use. (Experimental) |

### `name` field

The required `name` field:

- Must be 1-64 characters
- May only contain unicode lowercase alphanumeric characters and hyphens (`a-z` and )
- Must not start or end with
- Must not contain consecutive hyphens (`-`)
- Must match the parent directory name

Valid examples:

```yaml
name: pdf-processing

```

```yaml
name: data-analysis

```

```yaml
name: code-review

```

Invalid examples:

```yaml
name: PDF-Processing  # uppercase not allowed

```

```yaml
name: -pdf  # cannot start with hyphen

```

```yaml
name: pdf--processing  # consecutive hyphens not allowed

```

### `description` field

The required `description` field:

- Must be 1-1024 characters
- Should describe both what the skill does and when to use it
- Should include specific keywords that help agents identify relevant tasks

Good example:

```yaml
description: Extracts text and tables from PDF files, fills PDF forms, and merges multiple PDFs. Use when working with PDF documents or when the user mentions PDFs, forms, or document extraction.

```

Poor example:

```yaml
description: Helps with PDFs.

```

### `license` field

The optional `license` field:

- Specifies the license applied to the skill
- We recommend keeping it short (either the name of a license or the name of a bundled license file)

Example:

```yaml
license: Proprietary. LICENSE.txt has complete terms

```

### `compatibility` field

The optional `compatibility` field:

- Must be 1-500 characters if provided
- Should only be included if your skill has specific environment requirements
- Can indicate intended product, required system packages, network access needs, etc.

Examples:

```yaml
compatibility: Designed for Claude Code (or similar products)

```

```yaml
compatibility: Requires git, docker, jq, and access to the internet

```

<Note>
Most skills do not need the `compatibility` field.
</Note>

### `metadata` field

The optional `metadata` field:

- A map from string keys to string values
- Clients can use this to store additional properties not defined by the Agent Skills spec
- We recommend making your key names reasonably unique to avoid accidental conflicts

Example:

```yaml
metadata:
  author: example-org
  version: "1.0"

```

### `allowed-tools` field

The optional `allowed-tools` field:

- A space-delimited list of tools that are pre-approved to run
- Experimental. Support for this field may vary between agent implementations

Example:

```yaml
allowed-tools: Bash(git:*) Bash(jq:*) Read

```

### Body content

The Markdown body after the frontmatter contains the skill instructions. There are no format restrictions. Write whatever helps agents perform the task effectively.

Recommended sections:

- Step-by-step instructions
- Examples of inputs and outputs
- Common edge cases

Note that the agent will load this entire file once it's decided to activate a skill. Consider splitting longer `SKILL.md` content into referenced files.

## Optional directories

### scripts/

Contains executable code that agents can run. Scripts should:

- Be self-contained or clearly document dependencies
- Include helpful error messages
- Handle edge cases gracefully

Supported languages depend on the agent implementation. Common options include Python, Bash, and JavaScript.

### references/

Contains additional documentation that agents can read when needed:

- `REFERENCE.md` - Detailed technical reference
- `FORMS.md` - Form templates or structured data formats
- Domain-specific files (`finance.md`, `legal.md`, etc.)

Keep individual [reference files](https://www.notion.so/Agent-Skills-2ead1319e87a81cd87c1c7a4f8098665?pvs=21) focused. Agents load these on demand, so smaller files mean less use of context.

### assets/

Contains static resources:

- Templates (document templates, configuration templates)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)

## Progressive disclosure

Skills should be structured for efficient use of context:

1. **Metadata** (\~100 tokens): The `name` and `description` fields are loaded at startup for all skills
2. **Instructions** (\< 5000 tokens recommended): The full `SKILL.md` body is loaded when the skill is activated
3. **Resources** (as needed): Files (e.g. those in `scripts/`, `references/`, or `assets/`) are loaded only when required

Keep your main `SKILL.md` under 500 lines. Move detailed reference material to separate files.

## File references

When referencing other files in your skill, use relative paths from the skill root:

```markdown
See [the reference guide](references/REFERENCE.md) for details.

Run the extraction script:
scripts/extract.py

```

Keep file references one level deep from `SKILL.md`. Avoid deeply nested reference chains.

## Validation

Use the [skills-ref](https://github.com/agentskills/agentskills/tree/main/skills-ref) reference library to validate your skills:

```bash
skills-ref validate ./my-skill

```

This checks that your `SKILL.md` frontmatter is valid and follows all naming conventions.

---

> To find navigation and other pages in this documentation, fetch the llms.txt file at: https://agentskills.io/llms.txt
> 

# Integrate skills into your agent

> How to add Agent Skills support to your agent or tool.
> 

This guide explains how to add skills support to an AI agent or development tool.

## Integration approaches

The two main approaches to integrating skills are:

**Filesystem-based agents** operate within a computer environment (bash/unix) and represent the most capable option. Skills are activated when models issue shell commands like `cat /path/to/my-skill/SKILL.md`. Bundled resources are accessed through shell commands.

**Tool-based agents** function without a dedicated computer environment. Instead, they implement tools allowing models to trigger skills and access bundled assets. The specific tool implementation is up to the developer.

## Overview

A skills-compatible agent needs to:

1. **Discover** skills in configured directories
2. **Load metadata** (name and description) at startup
3. **Match** user tasks to relevant skills
4. **Activate** skills by loading full instructions
5. **Execute** scripts and access resources as needed

## Skill discovery

Skills are folders containing a `SKILL.md` file. Your agent should scan configured directories for valid skills.

## Loading metadata

At startup, parse only the frontmatter of each `SKILL.md` file. This keeps initial context usage low.

### Parsing frontmatter

```
function parseMetadata(skillPath):
    content = readFile(skillPath + "/SKILL.md")
    frontmatter = extractYAMLFrontmatter(content)

    return {
        name: frontmatter.name,
        description: frontmatter.description,
        path: skillPath
    }

```

### Injecting into context

Include skill metadata in the system prompt so the model knows what skills are available.

Follow your platform's guidance for system prompt updates. For example, for Claude models, the recommended format uses XML:

```xml
<available_skills>
  <skill>
    <name>pdf-processing</name>
    <description>Extracts text and tables from PDF files, fills forms, merges documents.</description>
    <location>/path/to/skills/pdf-processing/SKILL.md</location>
  </skill>
  <skill>
    <name>data-analysis</name>
    <description>Analyzes datasets, generates charts, and creates summary reports.</description>
    <location>/path/to/skills/data-analysis/SKILL.md</location>
  </skill>
</available_skills>

```

For filesystem-based agents, include the `location` field with the absolute path to the [SKILL.md](http://skill.md/) file. For tool-based agents, the location can be omitted.

Keep metadata concise. Each skill should add roughly 50-100 tokens to the context.

## Security considerations

Script execution introduces security risks. Consider:

- **Sandboxing**: Run scripts in isolated environments
- **Allowlisting**: Only execute scripts from trusted skills
- **Confirmation**: Ask users before running potentially dangerous operations
- **Logging**: Record all script executions for auditing

## Reference implementation

The [skills-ref](https://github.com/agentskills/agentskills/tree/main/skills-ref) library provides Python utilities and a CLI for working with skills.

For example:

**Validate a skill directory:**

```
skills-ref validate <path>

```

**Generate `<available_skills>` XML for agent prompts:**

```
skills-ref to-prompt <path>...

```

Use the library source code as a reference implementation.

---

> To find navigation and other pages in this documentation, fetch the llms.txt file at: https://agentskills.io/llms.txt
>