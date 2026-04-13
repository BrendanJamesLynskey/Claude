# Anthropic Claude — Comprehensive Presentation Series

Interactive slide decks covering every aspect of Anthropic Claude — from first steps to advanced agent architectures, plus hands-on workflow walkthroughs with terminal session recordings. Built with Reveal.js.

**Landing page: https://brendanjameslynskey.github.io/Claude/**

---

## Presentations

| # | Topic | Description |
|---|-------|-------------|
| 01 | [Introduction to Claude](01-introduction/) | What Claude is, the model family, Constitutional AI, capabilities, pricing, and the competitive landscape |
| 02 | [Getting Started](02-getting-started/) | Creating an account, choosing an interface, first conversations, API keys, and initial setup |
| 03 | [Claude.ai — The Web Interface](03-claude-ai/) | Projects, Artifacts, extended thinking, styles, file uploads, team and enterprise features |
| 04 | [Claude Code — The CLI Tool](04-claude-code/) | Installation, core tools, slash commands, CLAUDE.md, hooks, MCP, sub-agents, memory, and Git integration |
| 05 | [The Claude API & SDKs](05-api-and-sdk/) | Messages API, Python and TypeScript SDKs, streaming, tool use, vision, batches, and prompt caching |
| 06 | [Integrations & Ecosystem](06-integrations/) | GitHub, VS Code, JetBrains, Cursor, MCP servers, AWS Bedrock, Vertex AI, Zapier, and enterprise patterns |
| 07 | [Agents & Multi-Agent Systems](07-agents/) | AI agents, agents.md, the Claude Agent SDK, multi-agent architectures, guardrails, and orchestration |
| 08 | [Productivity & Workflows](08-productivity/) | Prompt engineering, coding workflows, research, content creation, TDD, team patterns, and measuring ROI |
| 09 | [Future Capabilities & Roadmap](09-future/) | Longer context, computer use, persistent memory, advanced agents, safety advances, and Anthropic's mission |
| 10 | [Summary & Quick Reference](10-reference/) | Cheat sheets for models, pricing, commands, API, SDKs, prompt patterns, MCP config, and a full glossary |
| 11 | [Claude Desktop — The Native Claude App](11-claude-desktop/) | The native Claude app for macOS and Windows — Chat, Code, and Cowork tabs, connectors, shortcuts, enterprise controls, and an MCP-across-products matrix |
| 12 | [Claude Code Desktop — The Code Tab](12-claude-code-desktop/) | Deep dive on the Code tab — visual diffs, permission modes, Git worktrees, app preview, PR monitoring, computer use, and CLI/Desktop config differences by platform |
| 13 | [Claude Cowork — Autonomous Background Agents](13-claude-cowork/) | Deep dive on the Cowork tab — cloud VMs, computer use, Dispatch, parallel agents, budgets, and limits |
| 14 | [Claude Chat Desktop — The Chat Tab](14-claude-chat-desktop/) | Deep dive on the Chat tab — Projects, Artifacts, Styles, input modalities, Connectors, and the original stdio MCP surface via `claude_desktop_config.json` |

## Workflow Walkthroughs

Terminal session recordings that demonstrate Claude Code commands and workflows in action.

| Topic | Description |
|-------|-------------|
| [Custom Agents](15-workflow-custom-agents/) | Build, configure and deploy specialised sub-agents — creating agent definitions, triggering auto-delegation, and merging worktree results |
| [Hooks & Automation](16-workflow-hooks/) | Automated quality gates — auto-formatting with PostToolUse, blocking dangerous commands with PreToolUse, and enforcing conventional commits |
| [MCP Servers](17-workflow-mcp-servers/) | Extend Claude Code with external tool servers — adding GitHub and PostgreSQL MCP servers, then chaining MCP tools with built-in tools |
| [Multi-Agent Parallel Work](18-workflow-multi-agent/) | Worktree isolation and concurrent execution — launching multiple agents simultaneously, watching parallel progress, and merging results |
| [Project Bootstrap & Skills](19-workflow-project-bootstrap/) | From /init to custom slash commands — scaffolding CLAUDE.md, creating reusable /deploy and /component skills, and using project memory |

## Tutorials

| Topic | Description |
|-------|-------------|
| [Claude Code — GitHub PR Reviews and Fixes](https://github.com/BrendanJamesLynskey/claude-review-demo) | Step-by-step guide to setting up automated AI-powered pull request reviews and bug fixes using Claude Code and GitHub Actions |

## Reports

| Topic | Description |
|-------|-------------|
| [Certifications & Training](certifications-report/) | Advisory report on Claude AI credentials — official certs, cloud provider exams, free courses, productivity and employability analysis |

## Viewing

Each presentation is a self-contained `index.html` file. Open directly in a browser or via the landing page above. Navigate slides with arrow keys. Press `Esc` for the slide overview. Slides scroll vertically when content overflows.

## Tech Stack

- [Reveal.js 4.6.1](https://revealjs.com/) (CDN-hosted)
- Google Fonts: Playfair Display, DM Sans, JetBrains Mono
- Inline SVG diagrams — no external image dependencies
- Monokai syntax highlighting via Highlight.js

---

*Part of the [Software Engineering series](https://github.com/BrendanJamesLynskey/Miscellaneous#software-engineering).*
