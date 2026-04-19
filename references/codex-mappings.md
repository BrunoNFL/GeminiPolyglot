# ChatGPT Codex & GPTs Mappings

## Configuration Files
Since Codex instructions aren't standardized, the adapter looks for:
- `*.codexrules`: Specific project standards.
- `.chatgpt-prompt` or `.chatgpt-instructions.md`: Custom prompts stored as files.

## Custom Instruction Mapping
Gemini should treat these as "Project Rules" and apply them to the system context.

## Tool Mapping Table
When encountering a ChatGPT / Codex JSON tool definition (e.g., as defined in a GPT's "Actions"), map to Gemini tools:

| Codex / GPT Action Name | Gemini CLI Native Tool | Mapping Strategy |
|-------------------------|------------------------|------------------|
| `run_terminal`          | `run_shell_command`    | Ensure the script/command is executed in the workspace. |
| `edit_file` / `replace` | `replace`              | Map search/replace logic to Gemini `replace`. |
| `create_file`           | `write_file`           | Map the data and path. |
| `get_context` / `read`  | `read_file`            | Map `path` to `file_path`. |
| `grep` / `search`       | `grep_search`          | Map search criteria to `grep_search`. |
| `ask_question`          | `ask_user`             | Map the prompt to a structured `ask_user` call. |

## Intelligent Subagent Routing
When a Codex prompt or JSON function call attempts to delegate a task to a "sub-agent" or "secondary model", Gemini should route it to its native subagent set.

### Routing Logic Table:
| Codex Delegation Intent | Gemini Subagent |
|-------------------------|-----------------|
| "Analyze Codebase"      | `codebase_investigator` |
| "Refactor / Implement"  | `generalist` |
| "Design UI"             | `ui-designer` |
| "General Research"      | `generalist` |

### Function Calling Translation
- If a ChatGPT JSON "Functions" or "Tools" schema is found, Gemini CLI should attempt to translate it into a Gemini-native tool call or use an MCP integration if the tool is an external service.
- If the tool is local, Gemini should use `run_shell_command` to emulate the function's logic.

## Priority
`Gemini > Codex`.
