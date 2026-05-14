# BGI PR Architecture Risk Bot

GitHub Marketplace Action for architecture-aware PR review.

It scans the repo, builds `bgi-graph.json` and `fuse-graph.json`, then comments on the pull request with:

1. blast radius
2. high-coupling seams
3. impacted clusters
4. optional twin-based hints

## Inputs

- `github-token`
- `scan-path`
- `language`
- `graph-path`
- `fuse-graph-path`
- `index-db-path`
- `changed-files`
- `task-prompt`

## Example

```yaml
permissions:
  contents: read
  pull-requests: write

steps:
  - uses: actions/checkout@v4
  - uses: ahmedxuhri/bigindexer-pr-risk-bot@v0.1.0
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      task-prompt: "Review this PR for architecture risk."
```

## Compatibility

Tested against `bigindexer==0.1.3`.
