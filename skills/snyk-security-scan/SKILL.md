---
name: snyk-security-scan
description: "Scan code, dependencies, containers, IaC and SBOMs with Snyk via its first-party MCP server: authenticate, trust a folder, run the right scanner, read the findings, fix and re-scan. Includes package-health checks before adding a dependency."
version: 1.0.0
author: Nous Research
license: MIT
metadata:
  tags: "snyk, security, sast, sca, dependencies, vulnerabilities, container, iac, sbom, mcp"
  hermes_related_skills: "requesting-code-review, security-guidance"
---

# Snyk Security Scan Skill

Runs Snyk scanners against code you just wrote, dependencies you are about to add, container
images, IaC files and SBOMs, through the `snyk` MCP server bundled in this plugin. It does not
replace a code review; it finds known vulnerabilities, misconfigurations and risky packages and
tells you how to remediate them.

## When to Use

- After generating or editing source: `snyk_code_scan` (SAST) on the touched directory.
- Before adding or upgrading a dependency: `snyk_package_health_check`, then
  `snyk_breakability_check` for the upgrade path.
- On a project with a manifest/lockfile: `snyk_sca_scan` for known-vulnerable dependencies.
- Before pushing a Dockerfile/image or Terraform/Kubernetes/CloudFormation: `snyk_container_scan`,
  `snyk_iac_scan`.
- When handed an SBOM (CycloneDX/SPDX): `snyk_sbom_scan`.
- NOT for: secrets detection, dynamic testing, or scanning code you must not upload (scans send
  code metadata to Snyk's cloud, see Pitfalls).

## Prerequisites

- The `snyk` plugin installed and enabled (`hermes plugins install snyk`, `hermes plugins enable snyk`,
  then a NEW Hermes session so the MCP tools load). The server is launched with
  `npx -y snyk@<pinned> mcp -t stdio --profile full --DISABLE_ANALYTICS`, so Node.js/npm must be
  on `PATH`. Usage analytics are disabled in the manifest; the scans themselves are network calls.
- A Snyk account (free tier covers code + open-source scanning). Nothing is prompted at install:
  the `snyk_auth` tool runs Snyk's browser login on demand and stores the token in the Snyk CLI's
  own config, never in Hermes.
- For SCA, the project's package manager (npm, pip/uv, maven, gradle, ...) must be installed so
  Snyk can resolve the dependency tree.

## How to Run

Tool names are prefixed by Hermes: `mcp__agent_plugin_snyk_8cb0f11d__sn__<tool>` when installed
from the catalog (the hash derives from the install directory name; `tool_search` for "snyk"
finds them either way). The suffix after `__sn__` is the Snyk tool name used below.

1. `snyk_auth` once per machine (or when any scan reports "not authenticated"). Tell the user a
   browser window opens; wait for it to complete.
2. Resolve the ABSOLUTE path of the target (`terminal`: `pwd`). Every `path` argument must be
   absolute; relative paths fail.
3. If a scan returns a trust error, run `snyk_trust` with that absolute path, then retry.
   Only trust a folder the user asked you to scan.
4. Run the scanner. Start with `severity_threshold: "high"` on large trees to keep output
   readable; drop to `medium`/`low` when the user wants everything.
5. Read the findings, fix what you can (upgrade pins, patch the code), re-run the same scan and
   report the before/after issue counts.

## Quick Reference

| Goal | Tool | Key args |
|---|---|---|
| SAST on source | `snyk_code_scan` | `path` (abs), `severity_threshold`, `org` |
| Vulnerable deps | `snyk_sca_scan` | `path` (abs), `all_projects`, `dev`, `command` (Python: `python3` REQUIRED), `file` |
| Vet a package | `snyk_package_health_check` | `ecosystem` (npm/pypi/maven/golang/nuget), `package_name`, `package_version` |
| Upgrade risk | `snyk_breakability_check` | `package_name`, `package_version_from`, `package_version_to` |
| Container image | `snyk_container_scan` | `image` (`node:20-alpine`, image id, `oci-archive:x.tar`), `file` (Dockerfile), `exclude_base_image_vulns` |
| IaC | `snyk_iac_scan` | `path` (abs), `severity_threshold`, `scan` (TF plan: `resource-changes`) |
| SBOM | `snyk_sbom_scan` | `file` (abs, CycloneDX 1.4–1.6 / SPDX 2.3 JSON) |
| AI BOM (preview) | `snyk_aibom` | `path` (abs, Python project); gated feature, may be unavailable |
| Login / logout / version | `snyk_auth`, `snyk_logout`, `snyk_version` | none |
| Telemetry back to Snyk | `snyk_send_feedback` | opt-in only; never call unprompted |

`references/tools.md` has the full argument list for every tool.

## Procedure

**"Scan what I just wrote"**
1. `pwd` → absolute project root. 2. `snyk_code_scan` on the directory (not the single file:
   SAST needs data-flow context). 3. For each finding: cite file:line, the rule, and the fix;
   apply fixes the user agreed to. 4. Re-scan, report delta.

**"Is this package safe to add?"**
1. `snyk_package_health_check` with ecosystem + name (+ version if pinned). 2. Report
   vulnerabilities, maintenance status, popularity. 3. If replacing a version:
   `snyk_breakability_check` from→to. 4. Only then edit the manifest, then `snyk_sca_scan` the
   project.

**"Audit dependencies"**
1. `snyk_sca_scan` with `path`; monorepo → `all_projects: true`; Python → `command: "python3"`
   (mandatory for Python). 2. Group by severity, list the upgrade path Snyk proposes. 3. Apply
   upgrades, re-run tests, re-scan.

**"Check my Dockerfile / infra"**
- Image: build or pull it first, then `snyk_container_scan` with `image` and `file` pointing at
  the Dockerfile so remediation names the offending layer.
- IaC: `snyk_iac_scan` on the directory; for Terraform plans generate the JSON plan first.

## Pitfalls

- Relative paths fail every scanner; always resolve with `pwd` first.
- `snyk_trust` is a one-way grant on the user's machine. Run it only for the folder being
  scanned, after the user asked for the scan.
- Python SCA without `command: "python3"` returns nothing useful.
- Scanning uploads code metadata (and for SAST, code) to Snyk's cloud. Ask before scanning
  private or client repositories; do not scan directories containing secrets or `.env` files.
- `snyk_send_feedback` reports issue-count deltas back to Snyk. It is outbound telemetry; leave
  it alone unless the user explicitly wants it.
- The first `npx` launch downloads the pinned CLI (tens of MB); a slow first connection is
  normal. The version is pinned in `mcp.json`; bumping it is a reviewed change to this plugin.
- Free-tier accounts have monthly test limits; a "limit reached" error is not a bug.

## Verification

- `snyk_version` returns `1.1306.0` (the pinned CLI) and `snyk_auth` reports an authenticated
  account before the first scan.
- A scan on a known-vulnerable fixture (e.g. `npm init -y && npm i lodash@4.17.15`) reports
  findings; after upgrading, the re-scan reports fewer.
- `hermes plugins list` shows `snyk` enabled and a new session lists tools ending in
  `__sn__snyk_code_scan`.
