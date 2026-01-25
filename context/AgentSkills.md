# Agent Skills

> Create, manage, and share Skills in Claude Code to extend Claude's capabilities.

This guide shows how to create, use, and manage Agent Skills in Claude Code. For background on how Skills work across Claude products, see [What are Skills?](https://platform.claude.com/docs/agents-and-tools/agent-skills/overview).

A Skill is a markdown file that teaches Claude how to perform a specific task. Review PRs using your team's standards, generate commit messages in your preferred format, or query your company's database schema. Ask Claude for something that matches the Skill's purpose, and Claude automatically applies it.

## Create Your First Skill

This example creates a personal Skill that teaches Claude to explain code using visual diagrams and analogies. Unlike Claude's default explanations, this Skill ensures every explanation includes ASCII diagrams and real-world analogies.

<Steps>
  <Step title="Check available Skills">
    Before creating a Skill, check what Skills Claude already has access to:

    ```
    What Skills are available?
    ```

    Claude will list all currently loaded Skills. You may have none, or you may have plugins or organization Skills.
  </Step>

  <Step title="Create Skill directory">
    Create a directory for your Skill in the personal Skills folder. Personal Skills are available across all projects. (You can also create [project Skills](#where-skills-live) in `.claude/skills/` to share with your team.)

    ```bash  theme={null}
    mkdir -p ~/.claude/skills/explaining-code
    ```
  </Step>

  <Step title="Write SKILL.md">
    Every Skill requires a `SKILL.md` file. The file starts with YAML metadata between `---` markers that must include `name` and `description`, followed by markdown instructions that Claude follows when the Skill is activated.

    The `description` is particularly important because Claude uses it to decide when to apply the Skill.

    Create `~/.claude/skills/explaining-code/SKILL.md`:

    ```yaml  theme={null}
    ---
    name: explaining-code
    description: Explains code with visual diagrams and analogies. Use when explaining how code works, teaching about a codebase, or when the user asks "how does this work?"
    ---

    When explaining code, always include:

    1. **Start with an analogy**: Compare the code to something from everyday life
    2. **Draw a diagram**: Use ASCII art to show the flow, structure, or relationships
    3. **Walk through the code**: Explain step-by-step what happens
    4. **Highlight a gotcha**: What's a common mistake or misconception?

    Keep explanations conversational. For complex concepts, use multiple analogies.
    ```
  </Step>

  <Step title="Load and verify Skill">
    Skills are automatically loaded when created or modified. Verify that your Skill appears in the list:

    ```
    What Skills are available?
    ```

    You should see `explaining-code` listed with its description.
  </Step>

  <Step title="Test Skill">
    Open any file in your project and ask Claude a question that matches the Skill's description:

    ```
    How does this code work?
    ```

    Claude should ask to use the `explaining-code` Skill, then include analogies and ASCII diagrams in the explanation. If the Skill doesn't trigger, try rephrasing to include more keywords from the description like "explain how this works".
  </Step>
</Steps>

The rest of this guide covers how Skills work, configuration options, and troubleshooting.

## How Skills Work

Skills are **model-invoked**: Claude decides which Skills to use based on your request. You don't need to explicitly invoke a Skill. Claude automatically applies relevant Skills when your request matches their description.

When you send a request, Claude follows these steps to find and use relevant Skills:

<Steps>
  <Step title="Discovery">
    At startup, Claude loads only the name and description of each available Skill. This keeps startup fast while giving Claude enough context to know when each Skill might be relevant.
  </Step>

  <Step title="Activation">
    When your request matches a Skill's description, Claude asks to use the Skill. A confirmation prompt appears before the full `SKILL.md` is loaded into context. Because Claude reads these descriptions to find relevant Skills, [write descriptions](#skill-not-triggering) that include keywords users would naturally say.
  </Step>

  <Step title="Execution">
    Claude follows the Skill's instructions, loading referenced files or running bundled scripts as needed.
  </Step>
</Steps>

### Where Skills Live

Where you store a Skill determines who can use it:

| Location    | Path                                          | Applies To                           |
| :---------- | :-------------------------------------------- | :----------------------------------- |
| Enterprise  | See [managed settings](/iam#managed-settings) | All users in organization            |
| Personal    | `~/.claude/skills/`                           | User across all projects             |
| Project     | `.claude/skills/`                             | Everyone working in this repository  |
| Plugin      | Bundled with [plugins](/plugins)              | Everyone with the plugin installed   |

When two Skills have the same name, higher rows take precedence: managed overrides personal, personal overrides project, project overrides plugin.

### When to Use Skills vs Other Options

Claude Code provides several ways to customize behavior. Key difference: **Skills are triggered automatically by Claude based on your request**, while slash commands require you to explicitly type `/command`.

| Use This                         | When You Want To                                        | When It Runs                        |
| :------------------------------- | :------------------------------------------------------ | :---------------------------------- |
| **Skills**                       | Give Claude expertise (e.g., "review PR using our standards") | Claude chooses when relevant        |
| **[Slash commands](/slash-commands)** | Create reusable prompts (e.g., `/deploy staging`)       | Run by typing `/command`            |
| **[CLAUDE.md](/memory)**         | Set project-wide instructions (e.g., "use TypeScript strict mode") | Loaded in every conversation        |
| **[Subagents](/sub-agents)**     | Delegate tasks to separate context with its own tools   | Claude delegates or explicit invoke |
| **[Hooks](/hooks)**              | Run scripts on events (e.g., lint on file save)         | Fires on specific tool events       |
| **[MCP servers](/mcp)**          | Connect Claude to external tools and data sources       | Claude calls MCP tools as needed    |

**Skills vs subagents**: Skills add knowledge to the current conversation. Subagents run in a separate context with their own tools. Use Skills for instructions and standards, subagents when you need isolation or different tool access.

**Skills vs MCP**: Skills tell Claude *how* to use tools. MCP *provides* tools. For example, an MCP server connects Claude to your database, while a Skill teaches Claude your data models and query patterns.

<Note>
  For a deep dive into the architecture and real-world applications of Agent Skills, read [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills).
</Note>

## Configure Skills

This section covers Skill file structure, supporting files, tool restrictions, and deployment options.

### Write SKILL.md

The `SKILL.md` file is the only required file for a Skill. It consists of two parts: YAML metadata at the top (the section between `---` markers) and markdown instructions that tell Claude how to use the Skill:

```yaml  theme={null}
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
---

# Your Skill Name

## Instructions
Provide clear, step-by-step guidance for Claude.

## Examples
Show concrete examples of using this Skill.
```

#### Available Metadata Fields

The following fields are available in YAML frontmatter:

| Field              | Required | Description                                                                                                                                                                                                                     |
| :----------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`             | Yes      | Skill name. Must use only lowercase, numbers, and hyphens (max 64 chars). Should match directory name.                                                                                                                          |
| `description`      | Yes      | What the Skill does and when to use it (max 1024 chars). Claude uses this to decide when to apply the Skill.                                                                                                                    |
| `allowed-tools`    | No       | Tools Claude can use without asking permission when this Skill is activated. Supports comma-separated values or YAML-style lists. See [restrict tool access](#restrict-tool-access-with-allowed-tools).                        |
| `model`            | No       | [Model](https://docs.claude.com/docs/about-claude/models/overview) to use when this Skill is activated (e.g., `claude-sonnet-4-20250514`). Defaults to the conversation's model.                                                |
| `context`          | No       | Set to `fork` to run the Skill in a forked sub-agent context with its own conversation history.                                                                                                                                 |
| `agent`            | No       | Specifies the [agent type](/sub-agents#built-in-subagents) to use when `context: fork` is set (e.g., `Explore`, `Plan`, `general-purpose` or a custom agent name from `.claude/agents/`). Defaults to `general-purpose`. Only applies when combined with `context: fork`. |
| `hooks`            | No       | Defines hooks scoped to this Skill's lifecycle. Supports `PreToolUse`, `PostToolUse`, and `Stop` events.                                                                                                                        |
| `user-invocable`   | No       | Controls whether the Skill appears in the slash command menu. Does not affect the [`Skill` tool](/slash-commands#skill-tool) or auto-discovery. Defaults to `true`. See [control Skill visibility](#control-skill-visibility). |

For complete writing guidelines including validation rules, see the [best practices guide](https://docs.claude.com/docs/agents-and-tools/agent-skills/best-practices).

### Update or Delete Skills

To update a Skill, edit the `SKILL.md` file directly. To remove a Skill, delete its directory. Changes take effect immediately.

### Add Supporting Files with Progressive Disclosure

Skills share Claude's context window with conversation history, other Skills, and your requests. To keep context focused, use **progressive disclosure**: put essential information in `SKILL.md` and detailed reference materials in separate files that Claude reads only when needed.

This approach lets you bundle comprehensive documentation, examples, and scripts without consuming context upfront. Claude loads additional files only when the task requires them.

<Tip>For optimal performance, keep `SKILL.md` under 500 lines. If your content exceeds this, split detailed reference material into separate files.</Tip>

#### Example: Multi-File Skill Structure

Claude discovers supporting files through links in `SKILL.md`. The following example shows a Skill with detailed documentation in separate files and utility scripts that Claude can execute without reading:

```
my-skill/
├── SKILL.md (required - overview and navigation)
├── reference.md (detailed API docs - loaded when needed)
├── examples.md (usage examples - loaded when needed)
└── scripts/
    └── helper.py (utility script - executed, not loaded)
```

The `SKILL.md` file references these supporting files so Claude knows they exist:

````markdown  theme={null}
## Overview

[Essential instructions here]

## Additional resources

- For complete API details, see [reference.md](reference.md)
- For usage examples, see [examples.md](examples.md)

## Utility scripts

To validate input files, run the helper script. It checks for required fields and returns any validation errors:
```bash
python scripts/helper.py input.txt
```
````

<Tip>Keep references one level deep. Link directly from `SKILL.md` to reference files. Deeply nested references (file A links to file B which links to file C) may cause Claude to read files partially.</Tip>

**Bundle utility scripts for 0-context execution.** Scripts in the Skill directory can be executed without loading their contents into context. Claude executes the script and only the output consumes tokens. This is useful for:

* Complex validation logic too verbose to explain in prose
* Data processing more reliable with tested code than generated code
* Operations that benefit from consistency across uses

In `SKILL.md`, instruct Claude to execute, not read, the script:

```markdown  theme={null}
Run the validation script to check the form:
python scripts/validate_form.py input.pdf
```

For complete guidelines on structuring Skills, see the [best practices guide](https://docs.claude.com/docs/agents-and-tools/agent-skills/best-practices#progressive-disclosure-patterns).

### Restrict Tool Access with allowed-tools

Use the `allowed-tools` frontmatter field to restrict which tools Claude can use when the Skill is activated. You can specify tools as a comma-separated string or YAML list:

```yaml  theme={null}
---
name: reading-files-safely
description: Read files without making changes. Use when you need read-only file access.
allowed-tools: Read, Grep, Glob
---
```

Or use YAML-style list for better readability:

```yaml  theme={null}
---
name: reading-files-safely
description: Read files without making changes. Use when you need read-only file access.
allowed-tools:
  - Read
  - Grep
  - Glob
---
```

When this Skill is activated, Claude can only use the specified tools (Read, Grep, Glob) without asking permission. This is useful for:

* Read-only Skills that should not modify files
* Limited scope Skills: for example data analysis only, no file writing
* Security-sensitive workflows where you want to restrict capabilities

Omitting `allowed-tools` means the Skill does not restrict tools. Claude uses the standard permission model and may ask to approve tool usage.

<Note>
  `allowed-tools` is only supported for Skills in Claude Code.
</Note>

### Run Skills in a Forked Context

Use `context: fork` to run a Skill in an isolated sub-agent context with its own conversation history. This is useful for Skills that perform complex multi-step tasks without cluttering the main conversation:

```yaml  theme={null}
---
name: code-analysis
description: Analyze code quality and generate detailed reports
context: fork
---
```

### Define Hooks for Skills

Skills can define hooks that execute during the Skill's lifecycle. Use the `hooks` field to specify `PreToolUse`, `PostToolUse`, or `Stop` handlers:

```yaml  theme={null}
---
name: secure-operations
description: Perform operations with additional security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/security-check.sh $TOOL_INPUT"
          once: true
---
```

The `once: true` option runs the hook only once per session. After the first successful execution, the hook is removed.

Hooks defined in a Skill are scoped to that Skill's execution and are automatically cleaned up when the Skill completes.

See [Hooks](/hooks) for the full hook configuration format.

### Control Skill Visibility

Skills can be invoked in three ways:

1. **Manual invocation**: Type `/skill-name` in your prompt
2. **Programmatic invocation**: Claude invokes through the [`Skill` tool](/slash-commands#skill-tool)
3. **Auto-discovery**: Claude reads Skill descriptions and loads when relevant to conversation

The `user-invocable` field controls only manual invocation. Setting it to `false` hides the Skill from the slash command menu, but Claude can still invoke it programmatically or discover it automatically.

To block programmatic invocation via the `Skill` tool, use `disable-model-invocation: true` instead.

#### When to Use Each Setting

| Setting                            | Slash Menu | `Skill` Tool | Auto-Discovery | Use Case                                                     |
| :--------------------------------- | :--------- | :----------- | :------------- | :----------------------------------------------------------- |
| `user-invocable: true` (default)   | Shown      | Allowed      | Yes            | Skills you want users to invoke directly                     |
| `user-invocable: false`            | Hidden     | Allowed      | Yes            | Skills Claude can use but users shouldn't invoke manually    |
| `disable-model-invocation: true`   | Shown      | Blocked      | Yes            | Skills you want users to invoke but Claude shouldn't call programmatically |

#### Example: Model-Only Skill

Set `user-invocable: false` to hide a Skill from the slash menu while allowing Claude to invoke it programmatically:

```yaml  theme={null}
---
name: internal-review-standards
description: Apply internal code review standards when reviewing pull requests
user-invocable: false
---
```

With this setting, users won't see the Skill in the `/` menu, but Claude can still invoke it via the `Skill` tool or discover it automatically based on context.

### Skills and Subagents

There are two ways Skills and subagents can work together:

#### Give Subagents Access to Skills

[Subagents](/sub-agents) don't automatically inherit Skills from the main conversation. To give a custom subagent access to specific Skills, list them in the subagent's `skills` field:

```yaml  theme={null}
# .claude/agents/code-reviewer.md
---
name: code-reviewer
description: Review code for quality and best practices
skills: pr-review, security-check
---
```

Listed Skills are loaded into the subagent's context when it starts. Omitting the `skills` field means no Skills are preloaded for that subagent.

<Note>
  Built-in agents (Explore, Plan, general-purpose) do not have access to Skills. Only custom subagents defined in `.claude/agents/` with explicit `skills` fields can use Skills.
</Note>

#### Run a Skill in Subagent Context

Use `context: fork` and `agent` to run a Skill in a forked subagent with its own separate context. See [run Skills in a forked context](#run-skills-in-a-forked-context) for details.

### Deploy Skills

You can share Skills in several ways:

* **Project Skills**: Commit `.claude/skills/` to version control. Everyone who clones the repository gets the Skills.
* **Plugins**: To share Skills across multiple repositories, create a `skills/` directory with Skill folders containing `SKILL.md` files in a [plugin](/plugins). Distribute through [plugin marketplaces](/plugin-marketplaces).
* **Managed**: Administrators can deploy Skills organization-wide via [managed settings](/iam#managed-settings). See the managed Skill path in [where Skills live](#where-skills-live).

## Examples

These examples show common Skill patterns from minimal single-file Skills to multi-file Skills with supporting documentation and scripts.

### Simple Skill (Single File)

A minimal Skill requires only a `SKILL.md` file with frontmatter and instructions. This example helps Claude generate commit messages by reviewing staged changes:

```
commit-helper/
└── SKILL.md
```

```yaml  theme={null}
---
name: generating-commit-messages
description: Generates clear commit messages from git diffs. Use when writing commit messages or reviewing staged changes.
---

# Generating Commit Messages

## Instructions

1. Run `git diff --staged` to see changes
2. I'll suggest a commit message with:
   - Summary under 50 characters
   - Detailed description
   - Affected components

## Best practices

- Use present tense
- Explain what and why, not how
```

### Using Multiple Files

For complex Skills, use progressive disclosure to keep the main `SKILL.md` focused while providing detailed documentation in supporting files. This PDF processing Skill includes reference documentation, utility scripts, and uses `allowed-tools` to restrict Claude to specific tools:

```
pdf-processing/
├── SKILL.md              # Overview and quick start
├── FORMS.md              # Form field mappings and filling instructions
├── REFERENCE.md          # API details for pypdf and pdfplumber
└── scripts/
    ├── fill_form.py      # Utility to populate form fields
    └── validate.py       # Checks PDFs for required fields
```

**`SKILL.md`**:

````yaml  theme={null}
---
name: pdf-processing
description: Extract text, fill forms, merge PDFs. Use when working with PDF files, forms, or document extraction. Requires pypdf and pdfplumber packages.
allowed-tools: Read, Bash(python:*)
---

# PDF Processing

## Quick start

Extract text:
```python
import pdfplumber
with pdfplumber.open("doc.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

For form filling, see [FORMS.md](FORMS.md).
For detailed API reference, see [REFERENCE.md](REFERENCE.md).

## Requirements

Packages must be installed in your environment:
```bash
pip install pypdf pdfplumber
```
````

<Note>
  If a Skill requires external packages, list them in the description. You must install packages in your environment before Claude can use them.
</Note>

## Troubleshooting

### View and Test Skills

To check which Skills Claude has access to, ask Claude questions like "What Skills are available?" Claude loads the name and description of all available Skills into its context window when a conversation starts, so it can list which Skills it currently has access to.

To test a specific Skill, ask Claude to perform a task that matches the Skill's description. For example, if a Skill's description says "review pull requests for code quality", ask Claude to "review the changes in the current branch." Claude will automatically use the Skill when your request matches its description.

### Skill Not Triggering

The description field is how Claude decides whether to use a Skill. A vague description like "helps with documentation" doesn't give Claude enough information to match the Skill to relevant requests.

Good descriptions answer two questions:

1. **What does this Skill do?** List specific capabilities.
2. **When should Claude use it?** Include trigger terms users would mention.

```yaml  theme={null}
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

This description works because it names specific actions (extract, fill, merge) and includes keywords users would say (PDF, forms, document extraction).

### Skill Not Loading

**Check file paths.** Skills must be in the correct directory with the exact filename `SKILL.md` (case-sensitive):

| Type       | Path                                                  |
| :--------- | :---------------------------------------------------- |
| Personal   | `~/.claude/skills/my-skill/SKILL.md`                  |
| Project    | `.claude/skills/my-skill/SKILL.md`                    |
| Enterprise | See platform-specific path in [where Skills live](#where-skills-live) |
| Plugin     | `skills/my-skill/SKILL.md` inside plugin directory    |

**Check YAML syntax.** Invalid YAML in frontmatter prevents Skills from loading. Frontmatter must start with `---` on line 1 (no blank lines before), end with `---` before markdown content, and use spaces for indentation (not tabs).

**Run debug mode.** Use `claude --debug` to see Skill loading errors.

### Skill Has Errors

**Verify dependencies are installed.** If a Skill uses external packages, you must install them in your environment before Claude can use them.

**Check script permissions.** Scripts require execute permissions: `chmod +x scripts/*.py`

**Check file paths.** Use forward slashes (Unix-style) in all paths. Use `scripts/helper.py`, not `scripts\helper.py`.

### Multiple Skills Conflicting

If Claude uses the wrong Skill or seems confused between similar Skills, the descriptions are probably too similar. Differentiate each description with specific trigger terms.

For example, instead of both Skills having "data analysis", make one "sales data from Excel files and CRM exports" and the other "log files and system metrics". The more specific the trigger terms, the easier Claude can find the right Skill to match your request.

### Plugin Skills Not Appearing

**Symptom**: You installed a plugin from a marketplace but Skills don't appear when you ask Claude "What Skills are available?"

**Solution**: Clear the plugin cache and reinstall:

```bash  theme={null}
rm -rf ~/.claude/plugins/cache
```

Then restart Claude Code and reinstall the plugin:

```shell  theme={null}
/plugin install plugin-name@marketplace-name
```

This forces Claude Code to re-download and re-register the plugin's Skills.

**If Skills still don't appear**, verify the plugin's directory structure is correct. Skills must be in a `skills/` directory at the plugin root:

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── my-skill/
        └── SKILL.md
```



---

> To find navigation and other pages in this documentation, fetch the llms.txt file at: https://code.claude.com/docs/llms.txt
