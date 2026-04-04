# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.x     | Yes       |

## Reporting a Vulnerability

If you discover a security issue in this action or in the underlying scanner, please report it privately:

1. Do not open a public issue.
2. Email us at security@hol.org with details.
3. Include steps to reproduce, expected vs actual behavior, and potential impact.
4. If the issue is in the source scanner, include the corresponding `codex-plugin-scanner` version or commit when possible.
5. We will acknowledge within 48 hours and aim to resolve within 7 days.

## Scope

This repository is the Marketplace-facing GitHub Action bundle. Some issues belong here, and some belong in the source repository:

- Action-repository scope:
  - incorrect `action.yml` metadata
  - Marketplace-specific packaging mistakes
  - broken README examples for the published action
- Source-repository scope:
  - scanner findings, scoring, verification, or submission logic
  - CLI behavior
  - release automation that generates this bundle

Source repository:

- [hashgraph-online/codex-plugin-scanner](https://github.com/hashgraph-online/codex-plugin-scanner)

## Security Best Practices

This tool helps you follow security best practices for Codex plugins. For the latest guidance, see the [Codex Security documentation](https://developers.openai.com/codex/security).

### For Plugin Authors

- Never commit API keys, tokens, or secrets to your repository.
- Use environment variables for sensitive configuration.
- Avoid dangerous shell commands in `.mcp.json` configurations.
- Include a `SECURITY.md` in your plugin repository.
- Use permissive licenses (Apache-2.0 or MIT) for clarity.

### For Scanner Users

- This scanner checks for common patterns but does not guarantee security.
- Always review plugin code manually before installation.
- Keep this tool updated for the latest check definitions.
- Pin the action to a reviewed major or immutable release tag in your workflows.
