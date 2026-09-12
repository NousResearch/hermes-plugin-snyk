# hermes-plugin-snyk

Scan code, dependencies, container images, IaC and SBOMs from Hermes Agent
through [Snyk](https://snyk.io)'s first-party MCP server. Snyk ships the
server *inside* the Snyk CLI (`snyk mcp` is a vendor subcommand, not a
community wrapper), so this plugin is a pinned `npx` stdio launch plus a
workflow skill.

This is a portable [Agent Plugins v1](https://agent-plugins.org) package. One
install ships:

- `mcp.json` — the Snyk MCP server entry (`sn`), registered for every new
  session while the plugin is enabled. No `mcp_servers` edits in config.yaml.
- `skills/snyk-security-scan/` — the workflow skill: when to use which
  scanner, the auth/trust/scan/fix loop, pitfalls, and a full per-tool
  argument reference generated from the live server.

## Install

```bash
hermes plugins install snyk       # from the Nous plugin catalog
hermes plugins enable snyk
```

Then start a new Hermes session. Node.js/npm must be on `PATH` (the server is
launched with `npx -y snyk@1.1306.0 mcp -t stdio --profile full`). The first
launch downloads the pinned CLI.

No credentials are needed at install time. Ask Hermes to run the `snyk_auth`
tool before the first scan: it opens Snyk's browser login and stores the token
in the Snyk CLI's own config, never in Hermes. A free Snyk account covers the
code (SAST) and open-source (SCA) scanners.

Health check without Hermes:

```bash
npx -y snyk@1.1306.0 mcp -t stdio --profile full --DISABLE_ANALYTICS <<'EOF'
{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"probe","version":"0"}}}
EOF
```

## What you get

13 tools, all listed in `skills/snyk-security-scan/references/tools.md`:
`snyk_code_scan` (SAST), `snyk_sca_scan` (dependencies), `snyk_container_scan`,
`snyk_iac_scan`, `snyk_sbom_scan`, `snyk_aibom`, `snyk_package_health_check`,
`snyk_breakability_check`, `snyk_auth`, `snyk_trust`, `snyk_logout`,
`snyk_version`, `snyk_send_feedback`.

Hermes exposes them as `mcp__agent_plugin_snyk_<hash>__sn__<tool>`. The
server key is `sn` rather than `snyk` because the longest tool name
(`snyk_package_health_check`) lands exactly on the 64-character function-name
cap of OpenAI-compatible wire formats with a `snyk` key; `sn` keeps every
name at 62 or less.

## Privacy and telemetry

- Snyk CLI usage analytics are disabled at launch (`--DISABLE_ANALYTICS` and
  `SNYK_DISABLE_ANALYTICS=1`).
- `snyk_send_feedback` reports issue-count deltas back to Snyk. The skill
  tells the agent to treat it as opt-in and never call it unprompted.
- The scans themselves are network calls: SAST uploads source, SCA uploads
  the dependency graph, to Snyk's cloud for analysis. The skill tells the
  agent to ask before scanning private repositories.
- `snyk_trust` grants Snyk permission to scan a folder on the user's machine.
  The skill restricts it to the folder the user asked to scan.

## Version pinning

`mcp.json` pins `snyk@1.1306.0` (published 2026-07-09). Bumping the pin is a
commit to this repo followed by a reviewed SHA bump in the
[hermes-agent plugin catalog](https://github.com/NousResearch/hermes-agent/tree/main/plugin-catalog).
`@latest` is deliberately not used.

## Attribution

Catalog research, tool pruning rationale and validation originate from
[hermes-agent #73860](https://github.com/NousResearch/hermes-agent/pull/73860),
re-homed here as a standalone plugin per the hermes-agent policy that
third-party product integrations ship outside the core tree. Snyk and the
Snyk CLI are products of Snyk Ltd. MIT.
