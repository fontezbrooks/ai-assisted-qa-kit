# MCP Setup for AI-Assisted QA

MCP (Model Context Protocol) servers give your AI assistant *real* access to the things
it needs to write good tests — a live browser, your test database, your PRs/CI, your
existing test files — instead of guessing. This turns "generate a plausible test" into
"generate a test grounded in the actual app."

See `mcp-config.example.json` for ready-to-paste server blocks.

## The four servers and why they matter for testing

| Server | What it unlocks for QA |
|---|---|
| **playwright** | The assistant drives a real browser, inspects the live DOM, and captures **stable locators** — eliminating the #1 cause of flaky generated tests (guessed selectors). |
| **postgres-testdata** | Read-only access to a **test** DB lets the assistant generate SQL data-validation checks and provision deterministic data. Never point it at production. |
| **github** | Reads PRs, issues, and CI logs so the assistant can turn a bug report into a regression test and debug a red CI run with the actual trace. |
| **filesystem** | Scoped access to your test project so the assistant reads existing page objects/fixtures **before** generating new ones — no duplication, consistent style. |

## Setup (Claude Code)
1. Open your MCP config (`~/.claude.json` or project `.mcp.json`).
2. Copy the server block(s) you want from `mcp-config.example.json`.
3. Replace every `<...>` placeholder. Put tokens in environment variables, not the file.
4. Restart Claude Code; confirm the servers connect.

## Setup (Cursor)
1. Settings → MCP → Add new server.
2. Paste the `command`/`args` from the example for each server.
3. Add tokens via environment variables.

## Safety
- **Test/staging only** for database and write-capable servers. Never connect production.
- Keep the GitHub token **least-privilege** (read-only on the repos you test).
- Scope the filesystem server to your test directory, not your home folder.
