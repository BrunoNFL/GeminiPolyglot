# Claude Rule & Command Mappings

## Configuration Files
The GeminiPolyglot looks for:
- `.mcp.json`: Standard MCP server configurations.
- `*.ts` or `*.js` files using the `tool()` function from `@anthropic-ai/claude-code`.

## Claude CLI Command Emulation
The adapter maps Claude CLI commands to Gemini actions using `scripts/claude_compat.sh`.

### Mapped Commands:
- `/compact`: Gemini should run a context cleanup or recommend a summary of the current session.
- `/cost`: Gemini explains cost tracking via the native CLI.
- `/help`: Gemini displays its native help but acknowledges the use of the Claude command.

## Intelligent Subagent Routing
When an instruction or tool call attempts to "spawn a Claude subagent", Gemini should route it to its own subagent set.

### Routing Logic Table:
| Claude Subagent Task | Gemini Subagent |
|----------------------|-----------------|
| "Analysis", "Investigation", "Root-cause" | `codebase_investigator` |
| "Refactoring", "Feature Implementation" | `generalist` |
| "UI/UX", "Visuals", "Design" | `ui-designer` |
| "General Questions", "Summarization" | `generalist` |

## Tool Mapping Table
When encountering Claude Code tool definitions or calls, Gemini MUST map them to the following native tools:

| Claude Tool / SDK Function | Gemini CLI Native Tool | Mapping Notes |
|----------------------------|------------------------|---------------|
| `AskUserQuestion`          | `ask_user`             | Map the prompt/question to `ask_user` with `type: 'text'`. |
| `Bash` / `RunCommand`      | `run_shell_command`    | Ensure the `command` argument is passed correctly. |
| `ReadFile` / `Read`        | `read_file`            | Map `path` to `file_path`. Use `start_line`/`end_line` for partial reads. |
| `WriteFile` / `Write`      | `write_file`           | Map `path` to `file_path` and `content` to `content`. |
| `SearchFiles` / `Grep`     | `grep_search`          | Map the search pattern and path accordingly. |
| `ListFiles` / `ls`         | `list_directory`       | Map the directory path to `dir_path`. |
| `FindFiles` / `Glob`       | `glob`                 | Map the glob pattern to `pattern`. |
| `FileEdit` / `Replace`     | `replace`              | Map `old_string` and `new_string` for surgical edits. |

### Special Case: `AskUserQuestion`
Claude's `AskUserQuestion` typically takes a simple string. Gemini's `ask_user` is more structured. 
- **Mapping Strategy:** Create a single question in the `questions` array.
- **Header:** Use a concise context label (e.g., "Clarify", "Confirm").
- **Type:** Use `text` for open-ended questions or `yesno` for confirmations.

## Tool Schema Mapping
- Claude Code's `tool()` uses Zod for input schemas.
- Gemini should attempt to infer the tool's JSON Schema from the Zod structure when mapping to its own native tool definitions.

## Priority
`Gemini > Claude`.
