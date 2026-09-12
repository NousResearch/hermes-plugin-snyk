# Snyk MCP tools (pinned CLI 1.1306.0, `--profile full`)

Generated from a live `tools/list` handshake against the pinned server. Hermes exposes each as
`mcp__agent_plugin_snyk_<hash>__sn__<name>`. `*` marks required arguments.

## snyk_aibom

Generates an AI Bill of Materials (AIBOM) for Python software projects in CycloneDX v1.6 JSON format. This feature analyzes local Python projects to identify AI models, datasets, tools, and other AI-related components. Requires an active internet connection and access to the experimental feature (available to customers on request). The command must be run from within a Python project directory and requires the CLI from the preview release channel. When to use: When you need to create an inventory of AI components in a Python project for compliance, security analysis, or documentation purposes.

| Argument | Type | Description |
|---|---|---|
| `json_file_output` | string | Saves the AIBOM output as a JSON data structure to the specified file path. The target directory must exist and be writable. |
| `path`* | string | Positional argument for the *ABSOLUTE PATH* to the directory to be scanned. The path MUST be absolute and have the correct path separator. You can retrieve the absolute path by invoking `pwd` on the command line in the working directory. Example: `/a/my-project` on linux/macOS or, on Windows `C:\a\my-project`. |

## snyk_auth

Authenticate the user with Snyk. When to use When a snyk tool reports that the user is not authenticated or when authentication is required.

No arguments.

## snyk_breakability_check

Runs a breaking change assessment for a package version upgrade.

| Argument | Type | Description |
|---|---|---|
| `package_name`* | string | The name of the package to look up. For scoped npm packages, include the scope (e.g., '@angular/core'). For Maven packages, use 'groupId:artifactId' format. |
| `package_version_from`* | string | The specific version of the package to look up. This is the version before the upgrade. |
| `package_version_to`* | string | The specific version of the package to look up. This is the version after the upgrade. |

## snyk_code_scan

Performs Static Application Security Testing (SAST) directly from the Snyk MCP. It analyzes an application's source code with a SAST scan to identify security vulnerabilities and weaknesses without executing the code. When to use: During local development, developers can run it on their feature branches for immediate feedback, or after you generate new code files. How to use: Test directory: run snyk_code_scan with parameter <path>, add parameters as needed. Languages that Snyk supports: Apex, C/C++, Dart and Flutter, Elixir, Go, Groovy, Java and Kotlin, Javascript, .NET, PHP, Python, Ruby, Rust, Scala, Swift and Objective-C, Typescript, VB.NET

| Argument | Type | Description |
|---|---|---|
| `debug` | boolean | Enables debug logging for the SAST scan, providing more detailed output for troubleshooting. Use as `-d`. |
| `include_ignores` | boolean | Include ignored vulnerabilities in the output. |
| `org` | string | Specifies the Snyk Organization ID (or slug name) under which the test results should be associated. This can influence private test limits and ensures results are reported to the correct Snyk Organization. Default is from `snyk config` or Snyk account. |
| `path`* | string | Positional argument for the *absolute path* to a file or directory to scan. The path MUST be absolute and have the correct path separator. You can retrieve the absolute path by invoking `pwd` on the command line in the working directory. Example: `/a/my-project` on linux/macOS or, on Windows `C:\a\my-project` |
| `severity_threshold` | string | Reports only vulnerabilities that meet or exceed the specified severity level. Accepted values: `low`, `medium`, `high`. Snyk Code configuration issues do not use the `critical` severity level. |

## snyk_container_scan

Scans container images for known vulnerabilities in OS packages and application dependencies. How to use: Test image: <snyk_container_scan> `IMAGE`=`my-image:v1`. Test with Dockerfile for context: <snyk_container_scan> `IMAGE`=`my-image:v1` `file`=`absolute/path/to/Dockerfile`. Test and exclude base image vulns: <snyk_container_scan> `IMAGE`=`my-image:v1` `exclude_base_image_vulns`. Test OCI archive: <snyk_container_scan> `IMAGE`=`oci-archive:image.tar` `platform`=`linux/arm64`.

| Argument | Type | Description |
|---|---|---|
| `app_vulns` | boolean | Enables scanning for vulnerabilities in application dependencies packaged within the container image (e.g., npm packages, Maven JARs). Enabled by default in Snyk MCP versions 1.1090.0 and higher. Mutually exclusive with `exclude_app_vulns`. |
| `exclude_app_vulns` | boolean | Disables scanning for application vulnerabilities within the container image, focusing only on OS package vulnerabilities. Default is disabled (meaning app vulns are scanned by default in CLI v1.1090.0+). Mutually exclusive with `app_vulns`. |
| `exclude_base_image_vulns` | boolean | Instructs Snyk not to report vulnerabilities that are introduced *only* by the base image layers. This helps focus on vulnerabilities added by application layers. Works for OS packages only. Default false. |
| `exclude_node_modules` | boolean | If scanning a Node.js container image, this option controls scanning of `node_modules` directories. By default (CLI v1.1292.0+), `node_modules` are scanned; this flag would disable that specific scan if explicitly set to true, or confirm default behavior. |
| `fail_on` | string | Controls conditions for a non-zero exit code. `all`: fails if any fixable (upgrade or Snyk-provided patch) vulnerability is found. `upgradable`: fails only if a vulnerability has a direct upgrade path available from Snyk. Default is to fail on any Snyk-discoverable vulnerability. |
| `file` | string | Path to the Dockerfile used to build the image. Snyk uses this to offer more accurate remediation advice, potentially identifying the base image or specific instructions that introduced vulnerabilities. |
| `image`* | string | Positional argument for the container image to test. Can be an image name from a registry (e.g., `node:14-alpine`), a local image ID, or a path to a tarball (e.g., `docker-archive:image.tar`, `oci-archive:image.tar`). |
| `org` | string | Specifies the Snyk Organization ID (or slug name) for reporting and association of results. Default is the configured Snyk Org. |
| `platform` | string | For multi-architecture container images, specifies the platform (architecture/OS) to test (e.g., `linux/amd64`, `linux/arm64`). Default is auto-detected or image default. |
| `policy_path` | string | Manually provides the path to a `.snyk` policy file containing ignore rules. Default is `.snyk` in project root (if applicable). |
| `print_deps` | boolean | Prints the dependency tree (OS packages and application dependencies if scanned) to the console before analysis. |
| `project_name` | string | Specifies a custom name for the project in the Snyk UI if results are monitored or reported. Default is auto-generated. |
| `severity_threshold` | string | Reports only vulnerabilities at or above the specified severity level. Accepted values: `low`, `medium`, `high`, `critical`. |

## snyk_iac_scan

Analyzes Infrastructure as Code (IaC) files for security misconfigurations. Supports Terraform (.tf, .tf.json, plan files), Kubernetes (YAML, JSON), AWS CloudFormation (YAML, JSON), Azure Resource Manager (ARM JSON), and Serverless Framework. When to use: Locally by developers while writing IaC. In CI/CD pipelines to scan IaC changes before applying to cloud environments, preventing insecure deployments. The `report` option sends results to Snyk UI for ongoing visibility. How to use: Test directory: <snyk_iac_scan> `path`=`absolute/path/to/dir`. Test specific TF file: <snyk_iac_scan> `path`=`absolute/path/to/file.tf`. Test dir, report to UI: <snyk_iac_scan> `path`=`absolute/path/to/dir` `report` `org`=`my-org`. Test K8s configs, report to UI, high severity: <snyk_iac_scan> `path`=`./k8s/` `report` `target_name`=`prod-k8s` `severity_threshold`=`high`. Test with custom rules: `<snyk_iac_scan> `path`=`/absolute/path/to/infra/` `rules`=`rules.tar.gz`.

| Argument | Type | Description |
|---|---|---|
| `ignore_policy` | boolean | Ignores all policies defined in the `.snyk` file and on snyk.io for this scan. |
| `org` | string | Specifies the Snyk Organization ID (or slug name) for associating results. Default is configured org. |
| `path`* | string | Positional argument for the *absolute path* to a file or directory to scan. The path MUST be absolute and have the correct path separator. You can retrieve the absolute path by invoking `pwd` on the command line in the working directory. Example: `/a/my-project` on linux/macOS or, on Windows `C:\a\my-project` |
| `policy_path` | string | Manually specifies the path to a `.snyk` policy file. Default is `.snyk` in root. |
| `project_business_criticality` | string | Sets project business criticality attribute(s) in Snyk UI (e.g. `critical,high`). Used with `report`. |
| `project_environment` | string | Sets project environment attribute(s) in Snyk UI (e.g. `frontend,backend`). Used with `report`. |
| `project_lifecycle` | string | Sets project lifecycle attribute(s) in Snyk UI (e.g. `production,sandbox`). Used with `report`. |
| `project_tags` | string | Sets project tags in Snyk UI (e.g., `dept=finance`). Used with `report`. |
| `remote_repo_url` | string | Sets or overrides the remote repository URL for the project in Snyk UI. Used with `report`. |
| `report` | boolean | Shares test results with the Snyk Web UI, creating/updating a project for tracking IaC issues. Mutually exclusive with `rules`. |
| `rules` | string | Specifies path to a custom rules bundle (`.tar.gz`) from snyk-iac-rules SDK for scans against custom policies. Mutually exclusive with `report`. Default is Snyk default rules. |
| `scan` | string | For Terraform plan scanning only. Specifies analysis mode: `planned-values` (full planned state) or `resource-changes` (proposed changes/deltas). Default `resource-changes`. |
| `severity_threshold` | string | Reports only misconfigurations at or above the specified severity level (`low`, `medium`, `high`, `critical`). |
| `target_name` | string | Sets or overrides project name in Snyk Web UI when used with `report`. Precedence over `remote_repo_url` for naming if both used. |
| `target_reference` | string | Specifies a reference (e.g., branch name, commit hash) to differentiate IaC project version in Snyk UI when used with `report`. |
| `var_file` | string | For Terraform, loads a variable definitions file (`.tfvars`) from a path different from the scanned directory. |

## snyk_logout

Logs the Snyk MCP out of the current Snyk account by clearing the locally stored authentication token. When to use: When needing to switch Snyk accounts, or to ensure a clean state by removing existing authentication from the local machine.

No arguments.

## snyk_package_health_check

Retrieves package information and health metrics from Snyk's package intelligence API. Returns details about a package including security vulnerabilities, maintenance status, popularity metrics, and community health indicators. When to use: When evaluating a package before adding it as a dependency, when changing a package version, or when assessing the health and security of existing dependencies.

| Argument | Type | Description |
|---|---|---|
| `ecosystem`* | string | The package ecosystem. Must be one of: npm, golang, pypi, maven, nuget. |
| `package_name`* | string | The name of the package to look up. For scoped npm packages, include the scope (e.g., '@angular/core'). For Maven packages, use 'groupId:artifactId' format. |
| `package_version` | string | The specific version of the package to look up. If not provided, returns information about the package in general (latest version info). |

## snyk_sbom_scan

Analyzes an existing SBOM file for known vulnerabilities in its open-source components. Requires components in SBOM to be identified using PackageURLs (purls). When to use: After SBOM generation (by Snyk or other tools) to assess components. In CI/CD to test generated/received SBOMs. For vulnerability scanning of third-party software when only an SBOM is available. How to use: <snyk_sbom_scan> `file`=`/absolute/path/to/my_app.cdx.json`. Input Requirements: SBOMs in CycloneDX (JSON 1.4-1.6) or SPDX (JSON 2.3). Packages must have purls (types: apk, cargo, cocoapods, composer, deb, gem, generic, golang, hex, maven, npm, nuget, pub, pypi, rpm, swift). Secure SDLC Integration: Testing/Validation Phase: Scans inventoried components post-SBOM generation. Third-Party Risk Management: Assesses vulnerabilities from SBOMs of external software.

| Argument | Type | Description |
|---|---|---|
| `debug` | boolean | Outputs debug logs for troubleshooting. Alias `debug`. Use as `-d`. |
| `file`* | string | Required. Specifies the path to the SBOM document to be tested (CycloneDX JSON 1.4-1.6, SPDX JSON 2.3). |
| `org` | string | Specifies the Snyk Organization ID. Verify applicability with `snyk sbom test help`. |
| `severity_threshold` | string | Filters results to report only vulnerabilities at or above specified severity (`low`, `medium`, `high`, `critical`). Verify applicability with `snyk sbom test help`. Default reports all. |

## snyk_sca_scan

WE NEED TO USE THE ABSOLUTE PATH IN THE PATH ARGUMENT. Analyzes projects for open-source vulnerabilities and license compliance issues by inspecting manifest files (e.g., package.json, pom.xml, requirements.txt, uv.lock) to understand dependencies and then queries the Snyk vulnerability database. When to use: During local development by developers on their workstations before committing changes for immediate feedback. How to use: Test locally: run tool with at least the path parameter. Prerequisites: Project's package manager (e.g., Gradle, Maven, npm) must be installed for accurate dependency resolution.

| Argument | Type | Description |
|---|---|---|
| `all_projects` | boolean | Auto-detects and tests all supported package manager manifest files found within the current directory and its subdirectories. Ideal for monorepos or solutions containing multiple projects. Mutually exclusive with `maven_aggregate_project` for Maven. Default is true. |
| `all_sub_projects` | boolean | Tests all Gradle sub-projects in a multi-project build. |
| `assets_project_name` | boolean | For NuGet (.NET), uses project name from `project.assets.json` for PackageReference projects when testing solution (`.sln`) files. |
| `command` | string | For Python and only python YOU MUST USE THIS ARGUMENT. Mandatory, specifies the Python executable (e.g., `python3`, `python` or absolute path to python executable). |
| `configuration_matching` | string | For Gradle, filters Gradle configurations to scan using a REGEX. |
| `dev` | boolean | Includes development-only dependencies in the scan (e.g., `devDependencies` in npm, `:development` group in RubyGems). Supported for Maven, npm, and Yarn projects. Default is false (only production dependencies scanned). |
| `dotnet_runtime_resolution` | boolean | For .NET projects using Runtime Resolution Scanning (Early Access). |
| `dotnet_target_framework` | string | For .NET, specifies a target framework for multi-targeted .NET solutions (Early Access). |
| `exclude` | string | Comma-separated list of directory or file names to exclude from scanning when using `all_projects` or `yarn_workspaces`. Cannot include paths. Example: `exclude=node_modules,tests,build`. |
| `fail_fast` | boolean | When used with `all_projects`, the scan process will stop immediately upon encountering the first error in any of the sub-projects, reporting the error and exiting. Without this, Snyk attempts to scan all projects and reports errors at the end. |
| `fail_on` | string | Determines the conditions under which the `snyk test` command will exit with a non-zero code (indicating failure), specifically for CI/CD integration. `all`: fails if any Snyk-fixable vulnerability (upgrade or patch) exists. `upgradable`: fails if a vulnerability has a direct upgrade path. `patchable`: fails if a Snyk patch is available. Default is `all` (fails on any discoverable vulnerability meeting severity criteria). |
| `file` | string | Specifies the path to a particular package manifest file (e.g., `package.json`, `pom.xml`, `requirements.txt`, `uv.lock`) that Snyk should inspect. If not provided, Snyk attempts auto-detection. Mutually exclusive with `all_projects` |
| `gradle_sub_project` | string | Tests a specific Gradle sub-project. Alias: `sub-project`. |
| `ignore_policy` | boolean | Instructs Snyk to ignore all policies defined in the `.snyk` file, organization-level ignores, and project policies on snyk.io for this specific scan. |
| `include_ignores` | boolean | Include ignored vulnerabilities in the output. |
| `maven_aggregate_project` | boolean | For multi-module Maven projects. Scans all modules defined in the root `pom.xml`. Cannot be used with `all_projects`. |
| `org` | string | Specifies the Snyk Organization ID (or slug name) under which the test results should be reported and associated. Essential if belonging to multiple Snyk Orgs. Default is the org from `snyk config` or Snyk account. |
| `package_manager` | string | Specifies the package manager type when the `file` option points to a manifest file with a non-standard name (e.g., `req.txt` instead of `requirements.txt` for Python). Accepted values: `npm`, `maven`, `pip`, `yarn`, `gradle`, `composer`, `rubygems`, `nuget`, `golangdep`, `govendor`, `gomodules`, `uv`. Default is auto-detected. |
| `path`* | string | Positional argument for the *ABSOLUTE PATH* to a directory, or a package to scan. The path MUST be absolute and have the correct path separator. You can retrieve the absolute path by invoking `pwd` on the command line in the working directory. Example: `/a/my-project` on linux/macOS or, on Windows `C:\a\my-project`. |
| `policy_path` | string | Manually provides the path to a `.snyk` policy file if it's not located in the project root. Default is `.snyk` in project root. |
| `print_deps` | boolean | Prints the full dependency tree of the project to the console before the analysis begins. Useful for understanding the project structure. |
| `project_name` | string | Specifies a custom name for the project as it will appear in the Snyk UI if results are monitored or reported. Default is auto-generated (e.g., from manifest or directory name). |
| `prune_repeated_subdependencies` | boolean | Simplifies the displayed dependency tree by removing duplicate sub-dependencies. This can make the output cleaner for large projects but may not show all vulnerable paths. Default is false. |
| `remote_repo_url` | string | Sets or overrides the remote repository URL associated with the project. Useful if the local project is not a git repository or to associate the scan with a different remote. |
| `scan_all_unmanaged` | boolean | For Maven ecosystem. Auto-detects and tests all Maven, JAR, WAR, AAR files recursively. Often used with `file` to target specific unmanaged archives. |
| `severity_threshold` | string | Reports only vulnerabilities that meet or exceed the specified severity level. Useful for filtering noise or focusing on critical issues. Accepted values: `low`, `medium`, `high`, `critical`. |
| `show_vulnerable_paths` | string | Controls how many vulnerable dependency paths are displayed in the output. Accepted values: `none` (shows no paths), `some` (shows a few examples), `all` (shows all identified paths). |
| `skip_unresolved` | boolean | For Python, skips packages not found in the environment |
| `strict_out_of_sync` | string | Controls behavior for out-of-sync lockfiles for npm, pnpm, Yarn. Accepted values: `true`, `false`. Default `true` for npm/yarn, `false` for pnpm. |
| `target_reference` | string | Specifies a reference (e.g., branch name, version tag) to differentiate this specific scan or project version, especially when results are monitored. Useful for grouping projects in Snyk UI. Supported for Snyk Open Source (except with `unmanaged`). |
| `trust_policies` | boolean | Applies and uses ignore rules found within Snyk policy files present in the project's dependencies. By default, such rules are only shown as suggestions. |
| `unmanaged` | boolean | Enables scanning for C++ projects or other scenarios where dependencies are not managed by a standard package manager. Snyk attempts to identify dependencies based on file signatures. |
| `yarn_workspaces` | boolean | Detects and scans Yarn Workspaces. Use with `all_projects` for broader monorepo scanning. |

## snyk_send_feedback

Report ONLY the delta (this run only) of Snyk issues. Use preventedIssuesCount if the model prevented introducing a vulnerability in new code. Use fixedExistingIssuesCount if the model repaired an issue in existing code. When the calling tool/hook supplies specific Snyk vuln IDs, pass them in preventedIssueIds. Counts must NEVER be cumulative. Always send an absolute path.

| Argument | Type | Description |
|---|---|---|
| `fixedExistingIssuesCount`* | number | Delta count of issues FIXED in pre-existing code during THIS run only (not cumulative). |
| `path`* | string | Absolute path to the project root or subdirectory. Example: '/a/my-project' (Linux/macOS) or 'C:\a\my-project' (Windows). |
| `preventedIssueIds` | array | Optional list of Snyk vuln IDs that the caller detected and is asking the model to fix. Prefix each entry with scan type: 'sast:<ruleId>' (e.g. 'sast:javascript/SqlInjection') or 'sca:<snykId>' (e.g. 'sca:SNYK-JS-LODASH-1234567'). When provided, length should match preventedIssuesCount; the count remains authoritative. |
| `preventedIssuesCount`* | number | Delta count of issues AVOIDED in newly generated code during THIS run only (not cumulative). |

## snyk_trust

Trust a given folder to allow Snyk to scan it. ONLY RUN THIS TOOL IF INSTRUCTED TO DO SO.

| Argument | Type | Description |
|---|---|---|
| `path`* | string | Path to the project folder to trust (default is the absolute path of the current directory, formatted according to the operating system's conventions). |

## snyk_version

Displays the installed Snyk MCP version. When to use: To verify current CLI version for compatibility checks or when reporting issues.

No arguments.
