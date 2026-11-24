# issue-generator

Reusable GitHub Action that opens **contributor-friendly** issues with Gemini.

## Use from another repo

Add `.github/workflows/issues.yml`:

```yaml
name: Issue Generator

on:
  schedule:
    - cron: '0 9 * * 1'
  workflow_dispatch:
    inputs:
      context:
        description: Area to generate issues for
        required: true
        default: general codebase improvements
      issue_count:
        description: Number of issues (1-5)
        required: true
        default: '3'

permissions:
  contents: read
  issues: write

jobs:
  generate:
    uses: aprameyak/issue-generator/.github/workflows/generate.yml@main
    with:
      context: ${{ github.event.inputs.context || 'general codebase improvements' }}
      issue_count: ${{ github.event.inputs.issue_count || '3' }}
    secrets:
      GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
```

Then set the secret:

```bash
gh secret set GEMINI_API_KEY -R <owner>/<repo>
```

## Inputs

| Input | Default | Notes |
| --- | --- | --- |
| `context` | general codebase improvements | Focus area |
| `issue_count` | `3` | Clamped to 1–5 |
| `model` | `gemini-3.8-flash` | Gemini model id |

## Safety

- Skips titles that match or closely overlap open issues
- Only allows a fixed label allowlist
- Retries Gemini on 429 / 5xx
- Does not require write access beyond `issues: write`
