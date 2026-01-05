# Wormhole Shared Workflows

Central place for reusable GitHub Actions workflows used across Wormhole projects. Keep CI logic in one repo, import it into app repos with `workflow_call`, and update behavior in a single spot.

## Repository layout

- `.github/workflows/` — home for reusable workflows.
- `.github/workflows/wormhole-demo-typecheck.yml` — runs the project's typecheck script against the latest `@wormhole-foundation/sdk`, captures logs, and opens a GitHub issue when failures occur on scheduled or manual runs.

## Current workflows

### Wormhole - SDK Type Check (`wormhole-demo-typecheck.yml`)

This workflow is a “canary” check that validates type compatibility with the newest Wormhole SDK release:

- Installs your repo dependencies from the lockfile using the selected package manager.
- Temporarily overrides `@wormhole-foundation/sdk` to `@latest` for the run and logs the exact version pulled (e.g., `@4.7.1`).
- Runs your project’s typecheck script against that resolved SDK version and fails if there are any TypeScript/type errors.
- Defaults to npm, but can use Yarn by setting `package_manager: yarn` when calling the workflow.

Each run tests against the version of `@wormhole-foundation/sdk` published as `@wormhole-foundation/sdk@latest` at runtime and records the version in the logs so you know what was validated.

It catches things like removed/renamed types or API signature changes (new/changed parameters) that would make the repo no longer typecheck against the latest published `@latest` SDK.

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
    # Optional: set to yarn for repos that use yarn.lock
    # with:
    #   package_manager: yarn
```

### Notes
- Pin to a tag or commit SHA for stability; use `@main` only if you accept frequent updates.
- Match permissions to the called workflow (e.g., issue creation needs `issues: write`).
- Review each workflow for required scripts, inputs, and secrets before adopting (the typecheck workflow expects your project to support a `typecheck` script).

## Adding or updating workflows

- Add new reusable workflows under `.github/workflows/` and trigger them with `workflow_call`.
- Document required permissions, secrets, and any expected project scripts.
- Keep workflows minimal on secrets; prefer scoped tokens and least-privilege permissions.

## License

See the LICENSE file for details.
