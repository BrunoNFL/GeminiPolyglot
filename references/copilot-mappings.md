# GitHub Copilot Rule Mappings

## Configuration Files
The GeminiPolyglot looks for:
- `.github/copilot-instructions.md`: Global repository-wide standards.
- `.github/instructions/*.instructions.md`: Path-specific rules with YAML frontmatter.
- `.github/skills/*/SKILL.md`: Modular workflows and capabilities.

## Multi-Agent Logic
If `AGENTS.md` is found in the workspace, Gemini CLI should treat its instructions as primary (after Gemini native rules) for all agents.

## Translation Logic
Gemini should map Copilot's `applyTo` glob patterns to its own conditional instruction set.

## Tool Mapping Table
Copilot's skill tool calls (e.g., from `SKILL.md` or `@github-copilot` interactions) should be mapped to Gemini CLI tools:

| Copilot Tool Name | Gemini CLI Native Tool | Mapping Notes |
|-------------------|------------------------|---------------|
| `run_shell` / `sh`| `run_shell_command`    | Ensure `command` argument is correctly mapped. |
| `read_file`       | `read_file`            | Direct mapping to `file_path`. |
| `write_file`      | `write_file`           | Direct mapping to `file_path` and `content`. |
| `search_codebase` | `grep_search`          | Map the search pattern and context. |
| `ask_user`        | `ask_user`             | Map the question to a structured `ask_user` call. |
| `list_files`      | `list_directory`       | Map to `list_directory`. |
| `edit_file`       | `replace`              | Map to `replace` for surgical updates. |

## Intelligent Subagent Routing
When a Copilot skill or instruction attempts to delegate to another agent (e.g., `@workspace`, `@terminal`, or a sub-skill), Gemini should route it to its own subagent set.

### Routing Logic Table:
| Copilot Agent / Intent | Gemini Subagent |
|-------------------------|-----------------|
| `@workspace` / Analysis | `codebase_investigator` |
| `@terminal` / Execution | `generalist` |
| Feature Implementation  | `generalist` |
| UI/UX / Design          | `ui-designer` |

### Skills Translation
- Copilot's `tools: []` property should be mapped to the most equivalent Gemini tools (e.g., `run_shell_command`, `read_file`, `write_file`).
- YAML frontmatter metadata from Copilot `SKILL.md` files should be ingested into Gemini's skill-discovery mechanism.

## Priority
`Gemini > Copilot`.
