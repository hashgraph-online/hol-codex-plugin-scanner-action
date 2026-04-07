# Contributing

Thanks for helping maintain the Marketplace-facing bundle for `plugin-scanner` (legacy package alias: `codex-plugin-scanner`).

## What lives here

This repository is the dedicated GitHub Marketplace action wrapper for the scanner. The implementation source of truth lives in:

- [`hashgraph-online/ai-plugin-scanner`](https://github.com/hashgraph-online/ai-plugin-scanner)

Most functional changes should start in the source repository and then be published into this action repository through the release sync workflow.

## What changes belong here

Changes that are appropriate in this repository:

- README, Marketplace copy, and examples
- action metadata in [`action.yml`](./action.yml)
- release-only docs like this contributing guide
- Marketplace-facing issue triage and support text

Changes that should usually happen in the source repository first:

- scanner behavior
- CLI flags and output contracts
- action runner logic
- release automation that produces this bundle

## Local review checklist

Before opening a PR:

1. Keep the diff focused on the action repository surface.
2. Make sure README examples use the real slug: `hashgraph-online/hol-codex-plugin-scanner-action@v1`.
3. If `action.yml` changes, confirm the inputs and outputs documented in [`README.md`](./README.md) still match.
4. If release/version copy changes, keep it aligned with the latest published scanner release and Marketplace listing.

## Pull requests

- Use a focused title and description.
- Include screenshots when the change is mainly Marketplace or README presentation.
- Link the related source-repo PR when the update was generated from `ai-plugin-scanner`.

## Security

For vulnerability reports, follow [`SECURITY.md`](./SECURITY.md) and do not open public issues for undisclosed security bugs.
