# js-reverse-playbook

An agent skill that standardizes a front-end JavaScript reverse-engineering
workflow: page observation, runtime sampling, local rebuild, environment
patching, and evidence-driven output.

It is the companion skill to the **JS Reverse MCP** server
(`@yuanhuakk/js-reverse-mcp` on npm) — the skill encodes the methodology, the
MCP server provides the browser/debugging tools it drives.

## Install

```bash
npx skills add YuanHuakk/js-reverse-playbook
```

This installs the skill into your agent's skills directory (e.g.
`~/.claude/skills/`). Restart your agent to pick it up.

## Companion MCP server

The skill assumes the JS Reverse MCP tools are available. Add the server to your
MCP client:

```json
{
  "mcpServers": {
    "js-reverse": {
      "command": "npx",
      "args": ["-y", "@yuanhuakk/js-reverse-mcp@latest"]
    }
  }
}
```

Requires Node.js >= 22 and a locally installed Google Chrome.

## What's inside

- `SKILL.md` — the five-phase workflow
  (Observe → Capture → Rebuild → Patch → DeepDive).
- `references/` — operational references (tool defaults, task templates,
  local rebuild, env patching, instrumentation, AST deobfuscation, fallbacks,
  output contract, abstract cases, schemas).
- `references/project-docs/` — bundled methodology snapshot so the skill is
  self-contained.
- `references/task-template/` — a ready-to-copy task-artifact scaffold
  (`task.json`, `env/`, `run/`, `report.md`, …) for `artifacts/tasks/<task-id>/`.

## License

Apache-2.0
