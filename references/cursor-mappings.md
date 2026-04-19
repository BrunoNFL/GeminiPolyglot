# Cursor Rule Mappings

## Configuration Files
The GeminiPolyglot looks for the following Cursor files:
- `.cursorrules`: Root-level project instructions.
- `.cursor/rules/*.mdc`: Modular rules applying to specific file patterns.

## Translation Logic
Gemini should map Cursor's markdown-based rules directly into its "project_context".

### Glob Patterns in `.cursor/rules/*.mdc`
- When parsing a `.mdc` file with YAML frontmatter, Gemini must use the `globs` property to apply those rules only when working within files matching the pattern.
- If multiple `.mdc` files match a file, their rules should be concatenated.

### Imperative Mapping
- Cursor often uses "Always use..." or "Never use..." phrasing. These should be treated as high-priority "MUST" constraints by Gemini within the scope of that project.

## Tool Mapping Table
Cursor's internal tool calls (often found in `.cursorrules` instructions) should be mapped to Gemini CLI tools:

| Cursor Intent / Tool | Gemini CLI Native Tool | Mapping Notes |
|----------------------|------------------------|---------------|
| `Edit File` / `Patch`| `replace`              | Map to `replace` for surgical changes. |
| `Create File`        | `write_file`           | Map to `write_file`. |
| `Run Terminal`       | `run_shell_command`    | Ensure the command is executed in the current environment. |
| `Search Codebase`    | `grep_search`          | Map the search pattern and context. |
| `Read Context`       | `read_file`            | Map to `read_file` with line range if specified. |
| `List Files`         | `list_directory`       | Map to `list_directory`. |
| `Ask User`           | `ask_user`             | Map the question to a structured `ask_user` call. |

### Priority
`Gemini > Cursor`. If a Cursor rule contradicts a native Gemini project rule, Gemini wins.
