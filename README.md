# test-scorecard-github-dora

Test repository for Scorecard DORA metrics with GitHub

## Deployment test workflows

This repository includes GitHub Actions workflows to generate test deployment data through the GitHub Deployments API.

- `Create and Merge Deployment PR`:
  - Trigger: manual (`workflow_dispatch`)
  - Inputs:
    - `auto_merge` (boolean, default `true`)
    - `deployment_outcome` (`success` or `failure`, default `success`)
  - Behavior: creates a test branch + PR, updates a single marker file (`deployment-simulations/latest.md`), applies `deployment-test` label, and can automatically merge to `main` to trigger deployment workflow.
  - Outcome: if `deployment_outcome = failure`, workflow applies `deployment-fail` label; otherwise only `deployment-test` is applied.
- `Test Deployment (on PR Merge)`:
  - Trigger: pull request merged into `main` with `deployment-test` label
  - Behavior: creates a test deployment and then sets deployment status.
  - Status rule: add PR label `deployment-fail` to force a failed deployment; otherwise deployment is marked success.
- `Mark Deployment Success`:
  - Trigger: manual (`workflow_dispatch`)
  - Input: `deployment_id`
  - Behavior: marks an existing deployment as `success`.
- `Mark Deployment Failure`:
  - Trigger: manual (`workflow_dispatch`)
  - Input: `deployment_id`
  - Behavior: marks an existing deployment as `failure`.

### Testing flow

1. Run `Create and Merge Deployment PR` with `auto_merge = true` and pick `deployment_outcome` (`success` or `failure`).
2. Confirm `Test Deployment (PR Merge)` runs and creates deployment/status (only runs for PRs labeled `deployment-test`).
3. Optionally run `Mark Deployment Success` or `Mark Deployment Failure` using the deployment id to add additional status events.
