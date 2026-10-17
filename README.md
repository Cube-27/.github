# Cube-27 shared workflows

## claude-review

Opt-in Claude code review on a PR, run with `anthropics/claude-code-action` and Anthropic's `code-review` plugin,
billed to an Anthropic API key. It runs only when asked:

```sh
gh pr comment <n> --body "/claude-review"     # needs write access to the repo
gh workflow run claude-review.yml -f pr=<n>
```

To enable it in a repo:

1. Install the Claude GitHub App on the repo (https://github.com/apps/claude).
2. Add the repo secret `ANTHROPIC_API_KEY` (`gh secret set ANTHROPIC_API_KEY -R Cube-27/<repo>`).
3. Add `.github/workflows/claude-review.yml` to its default branch:

```yaml
name: claude-review
on:
  issue_comment:
    types: [created]
  workflow_dispatch:
    inputs:
      pr:
        description: PR number to review
        required: true
        type: number
permissions:
  contents: read
  pull-requests: write
  issues: write
  id-token: write
jobs:
  review:
    uses: Cube-27/.github/.github/workflows/claude-review.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

Comment and dispatch triggers run only from the default branch, so the caller works once it is merged.
