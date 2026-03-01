# Add Claude Code support and update README

## Summary

This PR adds first-class **Claude Code** support alongside existing Cursor configurations, and updates the README to reflect the full repository layout (Cursor + Claude Code + MCP).

## Changes

- **`.claude/`** – Claude Code configs mirroring Cursor: 13 agents, 6 commands, 6 skills, and `settings.json`.
- **`CLAUDE.md`** – Always-on context for Claude Code: agent/command/skill tables, MCP notes, Python and Terraform standards.
- **`.mcp.json`** – Project-level MCP server configuration (team-shared).
- **README.md** – Describes both Cursor and Claude Code, MCP setup, and includes quick-reference tables for agents, commands, and skills. Repository structure updated to include `.claude/`, `CLAUDE.md`, and `.mcp.json`.
- **CHANGELOG.md** – New entries for Claude Code support, `.mcp.json`, and README improvements.

## Motivation

Teams using both Cursor and Claude Code can reuse the same agents, commands, and skills without maintaining two separate config trees. The README now serves as a single entry point for either environment.

## Breaking changes

None.

## Checklist

- [x] README reflects current structure (Cursor, Claude Code, MCP)
- [x] Changelog updated
- [ ] All changes committed
- [ ] Branch pushed to remote
- [ ] PR created targeting `main`
- [ ] Labels and reviewers set (optional)

## Screenshots

N/A (documentation and config only).
