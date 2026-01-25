# Creating Custom Subagents

> Create and use specialized AI subagents in Claude Code for task-specific workflows and enhanced context management.

Subagents are specialized AI assistants that handle specific types of tasks. Each subagent runs in its own context window with custom system prompts, specific tool access, and independent permissions. When Claude encounters a task that matches a subagent's description, it delegates to that subagent, which operates independently and returns results.

Subagents help you:

* **Context Preservation** - Keep exploration and implementation separate from the main conversation
* **Constraint Enforcement** - Limit which tools a subagent can use
* **Configuration Reuse** - Reuse across projects through user-level subagents
* **Behavior Specialization** - Focused system prompts for specific domains
* **Cost Control** - Route tasks to faster, cheaper models like Haiku

Claude uses each subagent's description to determine when to delegate tasks. When creating a subagent, write a clear description so Claude knows when to use it.

Claude Code includes several built-in subagents like **Explore**, **Plan**, and **general-purpose**. You can also create custom subagents to handle specific tasks. This page covers [built-in subagents](#built-in-subagents), [how to create your own subagents](#quickstart-create-your-first-subagent), [full configuration options](#configure-subagents), [subagent work patterns](#work-with-subagents), and [example subagents](#example-subagents).

## Built-in Subagents

Claude Code includes built-in subagents that Claude uses automatically when appropriate. Each inherits permissions from the parent conversation with additional tool restrictions.

<Tabs>
  <Tab title="Explore">
    A fast read-only agent optimized for codebase search and analysis.

    * **Model**: Haiku (fast, low latency)
    * **Tools**: Read-only tools (access denied to Write and Edit tools)
    * **Purpose**: File search, code search, codebase exploration

    Claude delegates to Explore when it needs to search or understand the codebase without making changes. This keeps exploration results separate from the main conversation context.

    When invoking Explore, Claude specifies a thoroughness level: **quick** for targeted lookups, **medium** for balanced exploration, **very thorough** for comprehensive analysis.
  </Tab>

  <Tab title="Plan">
    A research agent used in [plan mode](/common-workflows#use-plan-mode-for-safe-code-analysis) to gather context before presenting a plan.

    * **Model**: Inherited from main conversation
    * **Tools**: Read-only tools (access denied to Write and Edit tools)
    * **Purpose**: Codebase research for planning

    When in plan mode and Claude needs to understand the codebase, it delegates research to the Plan subagent. This gathers necessary context while preventing infinite nesting (subagents cannot spawn other subagents).
  </Tab>

  <Tab title="General-purpose">
    A capable agent for complex multi-step tasks requiring both exploration and action.

    * **Model**: Inherited from main conversation
    * **Tools**: All tools
    * **Purpose**: Complex research, multi-step tasks, code modifications

    Claude delegates to general-purpose when tasks require both exploration and modification, complex reasoning to interpret results, or multiple dependent steps.
  </Tab>

  <Tab title="Other">
    Claude Code includes additional helper agents for specific tasks. These are typically called automatically, so you don't need to use them directly.

    | Agent              | Model   | When Claude Uses It                               |
    | :----------------- | :------ | :------------------------------------------------ |
    | Bash               | Inherit | Execute terminal commands in separate context     |
    | statusline-setup   | Sonnet  | When running `/statusline` to configure status bar |
    | Claude Code Guide  | Haiku   | When asking questions about Claude Code features  |
  </Tab>
</Tabs>

In addition to these built-in subagents, you can create your own subagents with custom prompts, tool restrictions, permission modes, hooks, and skills. The following sections show how to get started and customize your subagents.

## Quickstart: Create Your First Subagent

Subagents are defined as Markdown files with YAML frontmatter. You can [create them manually](#write-subagent-files) or use the `/agents` slash command.

This exercise walks you through creating a user-level subagent with the `/agent` command. The subagent will review code and suggest improvements to your codebase.

<Steps>
  <Step title="Open the subagent interface">
    In Claude Code, run:

    ```
    /agents
    ```
  </Step>

  <Step title="Create a new user-level agent">
    Select **Create new agent**, then select **User-level**. This saves the subagent to `~/.claude/agents/` making it available across all projects.
  </Step>

  <Step title="Generate with Claude">
    Select **Generate with Claude**. When prompted, describe your subagent:

    ```
    A code improvement agent that scans files and suggests improvements for readability, performance, and best practices. It should explain each issue, show the current code, and provide an improved version.
    ```

    Claude will generate the system prompt and configuration. Press `e` to open in editor if you want to customize.
  </Step>

  <Step title="Select tools">
    For a read-only reviewer, deselect everything except **Read-only tools**. Keeping all tools selected means the subagent inherits all tools available from the main conversation.
  </Step>

  <Step title="Select model">
    Choose the model for your subagent. For this example agent, select **Sonnet** which balances capability and speed for code pattern analysis.
  </Step>

  <Step title="Select color">
    Choose a background color for your subagent. This helps identify which subagent is running in the UI.
  </Step>

  <Step title="Save and try">
    Save the subagent. It's immediately available (no restart needed). Try it:

    ```
    Use the code-improver agent to suggest improvements for this project
    ```

    Claude will delegate to the new subagent, which scans the codebase and returns improvement suggestions.
  </Step>
</Steps>

You now have a subagent that can analyze codebases and suggest improvements across all projects on your machine.

You can also create subagents manually as Markdown files, define them via CLI flags, or deploy them through plugins. The following sections cover all configuration options.

## Configure Subagents

### Using the /agents command

The `/agents` command provides an interactive interface for managing subagents. Run `/agents` to:

* View all available subagents (built-in, user, project, plugin)
* Create new subagents with guided setup or Claude generation
* Edit existing subagent configuration and tool access
* Delete custom subagents
* Check which subagent is active when there are duplicates

This is the recommended way to create and manage subagents. For manual creation or automation, you can also add subagent files directly.

### Choose Subagent Scope

Subagents are Markdown files with YAML frontmatter. Store them in different locations based on scope. When multiple subagents share the same name, higher-priority locations take precedence.

| Location                   | Scope                      | Priority   | How to Create                           |
| :------------------------- | :------------------------- | :--------- | :-------------------------------------- |
| `--agents` CLI flag        | Current session            | 1 (highest) | Pass JSON when starting Claude Code    |
| `.claude/agents/`          | Current project            | 2          | Interactive or manual                   |
| `~/.claude/agents/`        | All projects               | 3          | Interactive or manual                   |
| Plugin's `agents/` directory | Where plugin is activated | 4 (lowest) | Installed with [plugins](/plugins)     |

**Project subagents** (`.claude/agents/`) are ideal for subagents specific to your codebase. Check them into version control so your team can collaborate and improve them.

**User subagents** (`~/.claude/agents/`) are personal subagents available across all projects.

**CLI-defined subagents** are passed as JSON when starting Claude Code. They only exist for that session and are not saved to disk, making them useful for quick testing or automation scripts:

```bash  theme={null}
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer. Focus on code quality, security, and best practices.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  }
}'
```

The `--agents` flag accepts JSON with the same fields as [frontmatter](#supported-frontmatter-fields). Use `prompt` for the system prompt (equivalent to the markdown body in file-based subagents). See the [CLI reference](/cli-reference#agents-flag-format) for full JSON format.

**Plugin subagents** are provided by [plugins](/plugins) you've installed. They appear in `/agents` alongside custom subagents. For more on creating plugin subagents, see [plugin components reference](/plugins-reference#agents).

### Write Subagent Files

Subagent files use YAML frontmatter for configuration followed by the system prompt in Markdown:

<Note>
  Subagents are loaded at session start. If you create a subagent by manually adding a file, restart the session or use `/agents` to load immediately.
</Note>

```markdown  theme={null}
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---

You are a code reviewer. When invoked, analyze the code and provide
specific, actionable feedback on quality, security, and best practices.
```

The frontmatter defines the subagent's metadata and configuration. The body becomes the system prompt that guides the subagent's behavior. Subagents receive only this system prompt (plus basic environment details like working directory). They don't receive the full Claude Code system prompt.

#### Supported Frontmatter Fields

The following fields are available in YAML frontmatter. Only `name` and `description` are required.

| Field             | Required | Description                                                                                                         |
| :---------------- | :------- | :------------------------------------------------------------------------------------------------------------------ |
| `name`            | Yes      | Unique identifier using lowercase and hyphens                                                                       |
| `description`     | Yes      | When Claude should delegate to this subagent                                                                        |
| `tools`           | No       | [Tools](#available-tools) the subagent can use. If omitted, inherits all tools                                      |
| `disallowedTools` | No       | Tools to deny, removed from inherited or specified list                                                             |
| `model`           | No       | [Model](#choose-a-model) to use: `sonnet`, `opus`, `haiku`, or `inherit`. Defaults to `sonnet`                      |
| `permissionMode`  | No       | [Permission mode](#permission-modes): `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, or `plan`           |
| `skills`          | No       | [Skills](/skills) to load into subagent's context at start. Full skill content is injected, not made available for invocation. Subagents don't inherit skills from parent conversation |
| `hooks`           | No       | [Lifecycle hooks](#define-hooks-for-subagents) scoped to this subagent                                              |

### Choose a Model

The `model` field controls which [AI model](/model-config) the subagent uses:

* **Model alias**: Use one of the available aliases: `sonnet`, `opus`, or `haiku`
* **inherit**: Use the same model as the main conversation (useful for consistency)
* **Omitted**: If not specified, uses the default model configured for subagents (`sonnet`)

### Control Subagent Capabilities

You can control what a subagent can do through tool access, permission modes, and conditional rules.

#### Available Tools

Subagents can use any of Claude Code's [internal tools](/settings#tools-available-to-claude). By default, subagents inherit all tools from the main conversation, including MCP tools.

To restrict tools, use the `tools` field (allow list) or `disallowedTools` field (deny list):

```yaml  theme={null}
---
name: safe-researcher
description: Research agent with restricted capabilities
tools: Read, Grep, Glob, Bash
disallowedTools: Write, Edit
---
```

#### Permission Modes

The `permissionMode` field controls how the subagent handles permission prompts. Subagents inherit permission context from the main conversation but can override the mode.

| Mode                | Behavior                                           |
| :------------------ | :------------------------------------------------- |
| `default`           | Standard permission checks with prompts            |
| `acceptEdits`       | Auto-accept file edits                             |
| `dontAsk`           | Auto-deny permission prompts (explicitly allowed tools still work) |
| `bypassPermissions` | Skip all permission checks                         |
| `plan`              | Plan mode (read-only exploration)                  |

<Warning>
  Use `bypassPermissions` with caution. It skips all permission checks, allowing the subagent to execute any action without approval.
</Warning>

If the parent uses `bypassPermissions`, this takes precedence and cannot be overridden.

#### Conditional Rules with Hooks

For more dynamic control over tool usage, use `PreToolUse` hooks to validate actions before execution. This is useful when you want to allow some actions of a tool while blocking others.

This example creates a subagent that validates commands before execution, allowing only read-only database queries:

```yaml  theme={null}
---
name: db-reader
description: Execute read-only database queries
tools: Bash
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-readonly-query.sh"
---
```

The validation script inspects `$TOOL_INPUT` and exits with a non-zero code to block write operations. See [define hooks for subagents](#define-hooks-for-subagents) for more hook configuration options.

#### Disable Specific Subagents

You can prevent Claude from using specific subagents by adding them to the `deny` array in [settings](/settings#permission-settings). Use the format `Task(subagent-name)` where `subagent-name` matches the subagent's name field.

```json  theme={null}
{
  "permissions": {
    "deny": ["Task(Explore)", "Task(my-custom-agent)"]
  }
}
```

This works for both built-in and custom subagents. You can also use the `--disallowedTools` CLI flag:

```bash  theme={null}
claude --disallowedTools "Task(Explore)"
```

See the [IAM documentation](/iam#tool-specific-permission-rules) for more on permission rules.

### Define Hooks for Subagents

Subagents can define [hooks](/hooks) that execute during the subagent's lifecycle. There are two ways to configure hooks:

1. **In subagent's frontmatter**: Define hooks that only run while that subagent is active
2. **In `settings.json`**: Define hooks that run from the main session when a subagent starts or stops

#### Hooks in Subagent Frontmatter

Define hooks directly in the subagent's markdown file. These hooks only run while that specific subagent is active and are cleaned up when it completes.

| Event         | Matcher Input | When It Fires                        |
| :------------ | :------------ | :----------------------------------- |
| `PreToolUse`  | Tool name     | Before the subagent uses a tool      |
| `PostToolUse` | Tool name     | After the subagent uses a tool       |
| `Stop`        | (none)        | When the subagent completes          |

This example validates Bash commands with a `PreToolUse` hook and runs a linter after file edits with `PostToolUse`:

```yaml  theme={null}
---
name: code-reviewer
description: Review code changes with automatic linting
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-command.sh $TOOL_INPUT"
  PostToolUse:
    - matcher: "Edit|Write"
      hooks:
        - type: command
          command: "./scripts/run-linter.sh"
---
```

The `Stop` hook in frontmatter is automatically converted to a `SubagentStop` event.

#### Project-Level Hooks for Subagent Events

Configure hooks in `settings.json` that respond to subagent lifecycle events from the main session. Use the `matcher` field to target specific agent types by name.

| Event           | Matcher Input   | When It Fires                    |
| :-------------- | :-------------- | :------------------------------- |
| `SubagentStart` | Agent type name | When a subagent starts execution |
| `SubagentStop`  | Agent type name | When a subagent completes        |

This example runs setup and cleanup scripts only when the `db-agent` subagent starts and stops:

```json  theme={null}
{
  "hooks": {
    "SubagentStart": [
      {
        "matcher": "db-agent",
        "hooks": [
          { "type": "command", "command": "./scripts/setup-db-connection.sh" }
        ]
      }
    ],
    "SubagentStop": [
      {
        "matcher": "db-agent",
        "hooks": [
          { "type": "command", "command": "./scripts/cleanup-db-connection.sh" }
        ]
      }
    ]
  }
}
```

See [hooks](/hooks) for the full hook configuration format.

## Work with Subagents

### Understand Automatic Delegation

Claude automatically delegates tasks based on the request's task description, the `description` field in subagent configuration, and current context. To encourage proactive delegation, include phrases like "use proactively" in your subagent's description field.

You can also explicitly request a specific subagent:

```
Use the test-runner subagent to fix failing tests
Have the code-reviewer subagent look at my recent changes
```

### Run Subagents in Foreground or Background

Subagents can run in foreground (blocking) or background (concurrent):

* **Foreground subagents** block the main conversation until complete. Permission prompts and clarifying questions (e.g., [`AskUserQuestion`](/settings#tools-available-to-claude)) are forwarded to the user.
* **Background subagents** run concurrently while you continue working. They inherit the parent's permissions and automatically deny anything not pre-approved. If a background subagent needs a permission it doesn't have or needs to ask a clarifying question, that tool call fails but the subagent continues. MCP tools are not available in background subagents.

If a background subagent fails due to missing permissions, you can [resume](#resume-subagents) it to retry in foreground with interactive prompts.

Claude decides whether to run a subagent in foreground or background based on the task. You can also:

* Ask Claude to "run this in the background"
* Press **Ctrl+B** to move a running task to background

### Common Patterns

#### Isolate Bulk Operations

One of the most effective uses for subagents is isolating tasks that generate large amounts of output. Running tests, fetching documentation, or processing log files can consume significant context. Delegating these to a subagent keeps detailed output in the subagent's context while only relevant summaries return to the main conversation.

```
Use a subagent to run the test suite and report only the failing tests with their error messages
```

#### Run Parallel Research

For independent investigations, spawn multiple subagents to work concurrently:

```
Research the authentication, database, and API modules in parallel using separate subagents
```

Each subagent explores its area independently, then Claude synthesizes the results. This works best when research paths don't depend on each other.

<Warning>
  When subagents complete, results are returned to the main conversation. Running many subagents that each return detailed results can consume significant context.
</Warning>

#### Chain Subagents

For multi-step workflows, ask Claude to use subagents sequentially. Each subagent completes its task and returns results to Claude, who passes relevant context to the next subagent.

```
Use the code-reviewer subagent to find performance issues, then use the optimizer subagent to fix them
```

### Choose Between Subagents and Main Conversation

**Use main conversation** when:

* Tasks require frequent back-and-forth or iterative refinement
* Multiple steps share significant context (plan → implement → test)
* Making quick, targeted changes
* Latency is critical. Subagents start fresh and may take time to gather context

**Use subagents** when:

* Tasks generate detailed output not needed in main context
* You want to enforce specific tool restrictions or permissions
* Tasks are self-contained and can return a summary

Consider [skills](/skills) when you want reusable prompts or workflows that run in the main conversation context rather than isolated subagent context.

<Note>
  Subagents cannot spawn other subagents. If your workflow needs nested delegation, use [skills](/skills) or [chain subagents](#chain-subagents) from the main conversation.
</Note>

### Manage Subagent Context

#### Resume Subagents

Each subagent invocation creates a new instance with fresh context. To continue an existing subagent's work instead of starting fresh, ask Claude to resume it.

Resumed subagents retain their full conversation history including all previous tool calls, results, and reasoning. The subagent continues exactly where it left off rather than starting fresh.

When a subagent completes, Claude receives the agent ID. To resume a subagent, ask Claude to continue the previous work:

```
Use the code-reviewer subagent to review the authentication module
[Agent completes]

Continue that code review and now analyze the authorization logic
[Claude resumes the subagent with full context from previous conversation]
```

You can also ask Claude for the agent ID if you want to reference it explicitly, and you can find IDs in transcript files at `~/.claude/projects/{project}/{sessionId}/subagents/`. Each transcript is saved as `agent-{agentId}.jsonl`.

For programmatic usage, see [subagents in Agent SDK](/agent-sdk/subagents).

Subagent transcripts are maintained independently from the main conversation:

* **Main conversation compression**: When the main conversation compresses, subagent transcripts are unaffected. They're stored in separate files.
* **Session persistence**: Subagent transcripts persist within the session. You can [resume subagents](#resume-subagents) after restarting Claude Code by resuming the same session.
* **Automatic cleanup**: Transcripts are cleaned up based on the `cleanupPeriodDays` setting (default: 30 days).

#### Auto-Compression

Subagents support auto-compression using the same logic as the main conversation. When a subagent's context approaches the limit, Claude Code summarizes earlier messages to free space while preserving important context.

Compression events are logged in the subagent transcript file:

```json  theme={null}
{
  "type": "system",
  "subtype": "compact_boundary",
  "compactMetadata": {
    "trigger": "auto",
    "preTokens": 167189
  }
}
```

The `preTokens` value shows how many tokens were used before compression occurred.

## Example Subagents

These examples demonstrate effective patterns for building subagents. Use as starting points or generate custom versions with Claude.

<Tip>
  **Best practices:**

  * **Design focused subagents:** Each subagent should excel at a specific task
  * **Write detailed descriptions:** Claude uses descriptions to decide when to delegate
  * **Limit tool access:** Grant only necessary permissions for security and focus
  * **Check into version control:** Share project subagents with your team
</Tip>

### Code Reviewer

A read-only subagent that reviews code without modifying it. This example shows designing a focused subagent with restricted tool access (no Edit or Write) and a detailed prompt specifying exactly what to look for and how to format output.

```markdown  theme={null}
---
name: code-reviewer
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability. Use immediately after writing or modifying code.
tools: Read, Grep, Glob, Bash
model: inherit
---

You are a senior code reviewer ensuring high standards of code quality and security.

When invoked:
1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately

Review checklist:
- Code is clear and readable
- Functions and variables are well-named
- No duplicated code
- Proper error handling
- No exposed secrets or API keys
- Input validation implemented
- Good test coverage
- Performance considerations addressed

Provide feedback organized by priority:
- Critical issues (must fix)
- Warnings (should fix)
- Suggestions (consider improving)

Include specific examples of how to fix issues.
```

### Debugger

A subagent that can analyze and fix issues. Unlike the code reviewer, this includes Edit because bug fixes require code modifications. The prompt provides a clear workflow from diagnosis to verification.

```markdown  theme={null}
---
name: debugger
description: Debugging specialist for errors, test failures, and unexpected behavior. Use proactively when encountering any issues.
tools: Read, Edit, Bash, Grep, Glob
---

You are an expert debugger specializing in root cause analysis.

When invoked:
1. Capture error message and stack trace
2. Identify reproduction steps
3. Isolate the failure location
4. Implement minimal fix
5. Verify solution works

Debugging process:
- Analyze error messages and logs
- Check recent code changes
- Form and test hypotheses
- Add strategic debug logging
- Inspect variable states

For each issue, provide:
- Root cause explanation
- Evidence supporting the diagnosis
- Specific code fix
- Testing approach
- Prevention recommendations

Focus on fixing the underlying issue, not the symptoms.
```

### Data Scientist

A domain-specific subagent for data analysis tasks. This example shows creating subagents for specialized workflows outside general coding tasks. It explicitly sets `model: sonnet` for more capable analysis.

```markdown  theme={null}
---
name: data-scientist
description: Data analysis expert for SQL queries, BigQuery operations, and data insights. Use proactively for data analysis tasks and queries.
tools: Bash, Read, Write
model: sonnet
---

You are a data scientist specializing in SQL and BigQuery analysis.

When invoked:
1. Understand the data analysis requirement
2. Write efficient SQL queries
3. Use BigQuery command line tools (bq) when appropriate
4. Analyze and summarize results
5. Present findings clearly

Key practices:
- Write optimized SQL queries with proper filters
- Use appropriate aggregations and joins
- Include comments explaining complex logic
- Format results for readability
- Provide data-driven recommendations

For each analysis:
- Explain the query approach
- Document any assumptions
- Highlight key findings
- Suggest next steps based on data

Always ensure queries are efficient and cost-effective.
```


---

> To find navigation and other pages in this documentation, fetch the llms.txt file at: https://code.claude.com/docs/llms.txt
