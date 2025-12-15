# Wormhole Shared Workflows

Central place for reusable GitHub Actions workflows used across Wormhole projects. Keep CI logic in one repo, import it into app repos with `workflow_call`, and update behavior in a single spot.

## Repository layout

- `.github/workflows/` — home for reusable workflows.
- `.github/workflows/.github/workflows/wormhole-demo-typecheck.yml` — runs `npm run typecheck` against the latest `@wormhole-foundation/sdk`, captures logs, and opens a GitHub issue when failures occur on scheduled or manual runs.

## Using a workflow from another repo

Reference workflows by path and ref in downstream repos:

```yaml
name: Typecheck against latest Wormhole SDK
on:
  workflow_dispatch:
  schedule:
    - cron: '0 12 * * *'

jobs:
  typecheck:
    uses: wormhole-foundation/workflows/.github/workflows/.github/workflows/wormhole-demo-typecheck.yml@main
    permissions:
      contents: read
      issues: write # required for issue creation on failure
```

### Notes
- Pin to a tag or commit SHA for stability; use `@main` only if you accept frequent updates.
- Match permissions to the called workflow (e.g., issue creation needs `issues: write`).
- Review each workflow for required scripts, inputs, and secrets before adopting (the typecheck workflow expects your project to support `npm run typecheck`).

## Adding or updating workflows

- Add new reusable workflows under `.github/workflows/` and trigger them with `workflow_call`.
- Document required permissions, secrets, and any expected project scripts.
- Keep workflows minimal on secrets; prefer scoped tokens and least-privilege permissions.

## License

See the LICENSE file for details.