# HOL Codex Plugin Scanner GitHub Action

[![GitHub Marketplace](https://img.shields.io/badge/GitHub_Marketplace-HOL_Codex_Plugin_Scanner-blue?logo=github)](https://github.com/marketplace/actions/hol-codex-plugin-scanner-action) [![GitHub Release](https://img.shields.io/github/v/release/hashgraph-online/hol-codex-plugin-scanner-action?display_name=tag&logo=github)](https://github.com/hashgraph-online/hol-codex-plugin-scanner-action/releases) [![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](https://github.com/hashgraph-online/hol-codex-plugin-scanner-action/blob/main/LICENSE) [![Scanner Source](https://img.shields.io/badge/source-codex--plugin--scanner-0A66C2?logo=github)](https://github.com/hashgraph-online/codex-plugin-scanner)

| ![](https://raw.githubusercontent.com/hashgraph-online/standards-sdk-py/main/Hashgraph-Online.png) | **The default CI gate for Codex plugins — lint locally, verify in CI, and ship publish-ready bundles.** |
| :---: | :--- |

Use this action after `$plugin-creator` and before publishing, review, or distribution. It catches packaging, security, and publish-readiness issues early, then emits machine-readable outputs you can use in PR gates, code scanning, and submission workflows.

## Why this action exists

OpenAI owns plugin creation with `$plugin-creator`. This action is the quality gate between creation and distribution:

1. **Create** your plugin with `$plugin-creator`.
2. **Validate locally** with `codex-plugin-scanner`.
3. **Gate pull requests** with this GitHub Action.
4. **Ship or submit** with confidence.

> The score (`0-100`) is a useful trust signal, but it is evidence — not the headline.

## 30-second setup

### 1) Local preflight (recommended)

```bash
pipx run codex-plugin-scanner lint .
pipx run codex-plugin-scanner verify .
```

### 2) Add the CI gate

```yaml
name: Plugin quality gate

on:
  pull_request:
  push:
    branches: [main]

jobs:
  plugin-quality-gate:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write

    steps:
      - uses: actions/checkout@v4

      - name: Validate Codex plugin
        uses: hashgraph-online/hol-codex-plugin-scanner-action@v1
        with:
          plugin_dir: "."
          min_score: 70
          fail_on_severity: high
          format: sarif
          output: codex-plugin-scanner.sarif

      - name: Upload SARIF
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: codex-plugin-scanner.sarif
```

## What this action does

- Lints plugin structure and metadata hygiene.
- Verifies install/runtime and publish-readiness surfaces.
- Fails CI based on score and/or severity policy.
- Emits report artifacts (`text`, `json`, `markdown`, `sarif`).
- Uses the same scanner source of truth as the published `codex-plugin-scanner` package.
- Optionally opens submission issues for awesome-list/registry workflows.

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `plugin_dir` | Path to the plugin directory to validate | `.` |
| `format` | Output format: `text`, `json`, `markdown`, `sarif` | `text` |
| `output` | Write report to this file path | `""` |
| `min_score` | Fail if score is below this threshold (0-100) | `0` |
| `fail_on_severity` | Fail on findings at or above this severity: `none`, `critical`, `high`, `medium`, `low`, `info` | `none` |
| `cisco_skill_scan` | Cisco skill-scanner mode: `auto`, `on`, `off` | `auto` |
| `cisco_policy` | Cisco policy preset: `permissive`, `balanced`, `strict` | `balanced` |
| `install_cisco` | Install Cisco skill-scanner dependency for live skill scanning | `false` |
| `submission_enabled` | Open submission issues for awesome-list and registry automation when the plugin clears the submission threshold | `false` |
| `submission_score_threshold` | Minimum score required before a submission issue is created | `80` |
| `submission_repos` | Comma-separated GitHub repositories that should receive the submission issue | `hashgraph-online/awesome-codex-plugins` |
| `submission_token` | Required when `submission_enabled` is `true`; use a token with `issues:write` access to submission repositories | `""` |
| `submission_labels` | Comma-separated labels to apply when creating submission issues | `plugin-submission` |
| `submission_category` | Listing category included in submission issue body | `Community Plugins` |
| `submission_plugin_name` | Override plugin name used in submission issue | `""` |
| `submission_plugin_url` | Override plugin repository URL used in submission issue | `""` |
| `submission_plugin_description` | Override plugin description used in submission issue | `""` |
| `submission_author` | Override plugin author used in submission issue | `""` |

## Outputs

| Output | Description |
|--------|-------------|
| `score` | Numeric score (0-100) |
| `grade` | Letter grade (A-F) |
| `submission_eligible` | `true` when plugin met submission threshold and passed severity gate |
| `submission_performed` | `true` when a submission issue was created or an existing one was reused |
| `submission_issue_urls` | Comma-separated submission issue URLs |
| `submission_issue_numbers` | Comma-separated submission issue numbers |

## Marketplace and release notes

- Marketplace listing: [HOL Codex Plugin Scanner](https://github.com/marketplace/actions/hol-codex-plugin-scanner-action)
- Release notes: [GitHub Releases](https://github.com/hashgraph-online/hol-codex-plugin-scanner-action/releases)
- Source scanner changelog: [codex-plugin-scanner releases](https://github.com/hashgraph-online/codex-plugin-scanner/releases)

## Core use cases

### 1) Local preflight

Catch packaging, metadata, and security issues before you push.

### 2) PR gate

Block regressions in pull requests with score/severity thresholds.

### 3) Submission preflight

Generate publish-readiness signals and issue payloads for directory/registry workflows.

## Additional examples

### Minimal PR gate

```yaml
- uses: hashgraph-online/hol-codex-plugin-scanner-action@v1
  with:
    plugin_dir: "."
    min_score: 70
```

### Markdown report for PR comments

```yaml
- uses: hashgraph-online/hol-codex-plugin-scanner-action@v1
  id: scan
  with:
    plugin_dir: "."
    format: markdown
    output: scan-report.md

- name: Comment PR
  uses: actions/github-script@v7
  with:
    script: |
      const fs = require('fs');
      const report = fs.readFileSync('scan-report.md', 'utf8');
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: report
      });
```

### Score 80+ and auto-file submission issue

```yaml
permissions:
  contents: read

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Scan plugin and submit if eligible
        id: scan
        uses: hashgraph-online/hol-codex-plugin-scanner-action@v1
        with:
          plugin_dir: "."
          min_score: 80
          fail_on_severity: high
          submission_enabled: true
          submission_score_threshold: 80
          submission_token: ${{ secrets.AWESOME_CODEX_PLUGINS_TOKEN }}

      - name: Show submission issue
        if: steps.scan.outputs.submission_performed == 'true'
        run: echo "${{ steps.scan.outputs.submission_issue_urls }}"
```

Use a fine-grained token with `issues:write` on `hashgraph-online/awesome-codex-plugins`.

## Release management

- Publish immutable releases (for example `v1.4.0`).
- Move the floating major tag `v1` to the latest compatible release.
- Keep this action in its own public repository for GitHub Marketplace publication.
- Configure `ACTION_REPO_TOKEN` in the source repository so `publish-action-repo.yml` can sync this root-ready bundle automatically.
- Optionally set `ACTION_REPOSITORY` in the source repository if the target repository should not be `hashgraph-online/hol-codex-plugin-scanner-action`.

## Support files

- [Contributing guide](https://github.com/hashgraph-online/hol-codex-plugin-scanner-action/blob/main/CONTRIBUTING.md)
- [Security policy](https://github.com/hashgraph-online/hol-codex-plugin-scanner-action/blob/main/SECURITY.md)
- [Source scanner repository](https://github.com/hashgraph-online/codex-plugin-scanner)

## Source of truth

The source bundle for this action lives in the main scanner repository under `action/`. Release artifacts from that repository should export a root-ready action bundle for the dedicated Marketplace repository.

## License

[Apache-2.0](https://github.com/hashgraph-online/codex-plugin-scanner/blob/main/LICENSE)
