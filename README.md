# Big Indexer PR Risk Bot

[![Latest Release](https://img.shields.io/github/v/release/ahmedxuhri/bigindexer-pr-risk-bot)](https://github.com/ahmedxuhri/bigindexer-pr-risk-bot/releases)
[![CI](https://github.com/ahmedxuhri/bigindexer-pr-risk-bot/actions/workflows/ci.yml/badge.svg)](https://github.com/ahmedxuhri/bigindexer-pr-risk-bot/actions/workflows/ci.yml)

GitHub Marketplace Action for architecture-aware PR review.

Part of the Big Indexer ecosystem. Main project:
https://github.com/ahmedxuhri/bigindexer

It scans the repo, builds `bgi-graph.json` and `fuse-graph.json`, then comments on the pull request with:

1. blast radius
2. high-coupling seams
3. impacted clusters
4. optional twin-based hints

## Quick answers (before you implement)

- **Is it production-ready?**  
  It is production-usable, and this repo is focused on stable action behavior for PR review workflows.

- **How does scan run inside GitHub?**  
  The action runs scan inside CI automatically: checkout -> install Big Indexer -> `bgi scan` -> compute risk -> post/update comment.

- **Why is a GitHub token needed?**  
  Only to write or update the PR comment. It uses built-in `${{ github.token }}` by default, so users do not need a personal token.

- **Can I run it without PR comments?**  
  Yes. Set `post-comment: "false"` and use the step summary only.

## Copy-paste workflow (recommended)

```yaml
name: pr-architecture-risk

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write

jobs:
  risk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: ahmedxuhri/bigindexer-pr-risk-bot@v0.1.2
        with:
          # optional; action already falls back to github.token
          github-token: ${{ github.token }}
          task-prompt: "Review this PR for architecture risk."
```

## Why token is needed

- Needed only for `issues/comments` API calls (create/update one PR comment).
- Use `${{ github.token }}` (recommended), not a personal access token.
- If `post-comment` is disabled, token is not required.

## Inputs

| Input | Purpose | Default |
|---|---|---|
| `github-token` | Token for comment upsert. Falls back to `github.token` if omitted. | `""` |
| `scan-path` | Path to scan. | `"."` |
| `language` | Scan language mode. | `"auto"` |
| `graph-path` | Output graph path. | `".bgi/bgi-graph.json"` |
| `fuse-graph-path` | Output fuse path. | `".bgi/fuse-graph.json"` |
| `index-db-path` | Optional index DB path. | `""` |
| `changed-files` | Optional explicit changed files list. | `""` |
| `task-prompt` | Optional prompt for twin hints. | `""` |
| `max-files` | Max changed files analyzed. | `"40"` |
| `max-seams` | Max seams per file. | `"8"` |
| `impact-depth` | Neighbor traversal depth. | `"2"` |
| `max-neighbors` | Max impacted units per file. | `"40"` |
| `post-comment` | Toggle PR comment posting. | `"true"` |

## Minimal example

```yaml
permissions:
  contents: read
  pull-requests: write

steps:
  - uses: actions/checkout@v4
  - uses: ahmedxuhri/bigindexer-pr-risk-bot@v0.1.2
    with:
      github-token: ${{ github.token }}
      task-prompt: "Review this PR for architecture risk."
```

## Compatibility

Tested against `bigindexer>=0.1.2,<0.2`.
