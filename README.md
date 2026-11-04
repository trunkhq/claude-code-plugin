# Trunk for Claude Code

Connect Claude Code to Trunk's HTTP MCP server with OAuth and three workflows:

| Skill            | Purpose                                                     |
| ---------------- | ----------------------------------------------------------- |
| `/trunk:inspect` | Explore repository source, ownership, and policy.           |
| `/trunk:propose` | Submit new work or revise an existing Change with evidence. |
| `/trunk:status`  | Inspect checks, decisions, and landing blockers.            |

The package contains only MCP configuration and skills. Trunk's API remains
responsible for authorization, persistence, verification, and landing gates.
There are no install scripts, background hooks, or bundled credentials.

## Install

In a terminal with Claude Code installed:

```sh
claude plugin marketplace add trunkhq/claude-code-plugin
claude plugin install trunk@trunk
```

Start Claude Code and authenticate the plugin's Trunk server through `/mcp`.

## Try from a checkout

Clone this repository, then run from its root with Claude Code installed:

```sh
git clone https://github.com/trunkhq/claude-code-plugin.git
cd claude-code-plugin
```

```sh
claude --plugin-dir .
```

In Claude Code, open `/mcp`, select the plugin's Trunk server, and authenticate
through the browser. Select the intended organization and repository access in
Trunk's consent flow. Then try:

```text
/trunk:inspect Explain the architecture of my repository
/trunk:propose Submit the change we just implemented as a draft
/trunk:status Show the checks and blockers for change_ID
```

Skills are also available for Claude to select when a matching task is requested.
If tools are missing, check the connection in `/mcp` and the integration's
repository grants in Trunk. Read access does not imply proposal access. Agent
connections cannot directly land Changes or approve them as a human.

## Install from the local marketplace

From the repository root:

```sh
claude plugin marketplace add .
claude plugin install trunk@trunk
```

Start a new Claude Code session and authenticate using `/mcp`. Installation uses
Claude Code's plugin cache; after package changes, bump the plugin version and
update the installed plugin, or use `--plugin-dir` for development. Avoid loading
both the installed plugin and the development copy in the same session.

The root `.claude-plugin/marketplace.json` points to the repository root (`./`).
The plugin package is self-contained and does not require a Trunk source checkout.

## Self-hosted and local Trunk

The default endpoint is `https://api.trunk.dev/mcp`. Set the full API MCP URL
before launching Claude Code to target another installation:

```sh
TRUNK_MCP_URL=https://api.example.com/mcp claude --plugin-dir .
```

For local development, use `TRUNK_MCP_URL=http://localhost:4244/mcp` with the API
and Dashboard running and OAuth origins configured for that environment. Use the
API origin, not the Dashboard or Marketing origin. Leave the variable unset for
hosted Trunk; an empty value is not a valid endpoint. Reauthenticate in `/mcp`
after switching installations.

OAuth credentials are managed by Claude Code. Do not put tokens in the plugin,
commit them to a project, or paste them into a skill prompt. If you already have
a manually configured Trunk MCP server, disable the duplicate connection in
`/mcp` so the intended identity and endpoint are unambiguous.

## Validate

From the repository root:

```sh
claude plugin validate .claude-plugin/plugin.json
claude plugin validate .claude-plugin/marketplace.json
```

Then load with `--plugin-dir`, authenticate, and run `/trunk:inspect` against an
authorized repository. Manifest validation does not prove live OAuth or tool
execution. Use a disposable repository for proposal and Ready smoke tests;
Ready can allow normal automatic landing when all gates pass.

Packaging follows the official Claude Code
[plugin manifest](https://code.claude.com/docs/en/plugins-reference),
[marketplace](https://code.claude.com/docs/en/plugin-marketplaces), and
[MCP](https://code.claude.com/docs/en/mcp) documentation.
