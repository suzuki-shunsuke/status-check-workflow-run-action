# status-check-workflow-run-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/status-check-workflow-run-action/main/LICENSE) | [action.yaml](action.yaml)

`status-check-workflow-run-action` is a GitHub Action to create GitHub's Required Status Checks via `workflow-run`.
You can manage GitHub's Required Status Checks easily and securely.

## Background

To prevent pull requests whose CI don't pass from being merged, we should configure Branch Rulesets properly.

1. Enable `Require status checks to pass`
1. Add jobs to `Status checks that are required`

But if you add all jobs to `Status checks that are required`, you need to update both workflows and `Status checks that are required` every time you want to add, remove, or rename jobs.
It's inconvenient.
Furthermore, if you forget to add jobs to `Status checks that are required`, pull requests can be merged even if those jobs fail.
It's undesirable.

This action solves these problems.

## How To Use

1. Merge `pull_request` workflows into one workflow
1. Add `workflow_run` workflow
1. Add `status-check` to `Status checks that are required`

First, you need to merge `pull_request` workflows into one workflow.
If you use workflow's Path filters [on.<push|pull_request|pull_request_target>.<paths|paths-ignore>](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#onpushpull_requestpull_request_targetpathspaths-ignore), you can replace it with [dorny/paths-filter](https://github.com/dorny/paths-filter) is available.

After you merge `pull_request` workflows into one workflow, please add the following workflow.

```yaml
name: Status Check
on:
  workflow_run:
    workflows: [test] # pull_request workflow name
    types:
      - completed
jobs:
  status-check:
    runs-on: ubuntu-24.04
    permissions:
      statuses: write # To update commit statuses
    steps:
      - uses: suzuki-shunsuke/status-check-workflow-run-action@79471ff024007dbc192e6ce0576f71960b1c144d # v0.1.1
```

Finally, please add `status-check` to `Status checks that are required`.
You don't need to add other checks.

That's it.

The workflow `Status Check` is run after a `pull_request` workflow is completed, and it creates a commit status associated with the pull request's head commit.
The commit status is same with `pull_request` workflow's `conclusion`, i.e. if `pull_request` workflow succeeds the status check is `success`, otherwise the status check is `failure`.
So you can merge the pull request only if `pull_request` workflow passes.

## Inputs / Outputs

Please see [action.yaml](action.yaml) too.

All inputs are optional.

- `github_token`: A GitHub Access token to create commit statuses. The permission `statuses:write` is required. `workflow_run` is run in the context of the default branch, so it can't be tampered in feature branches. So it's secure
- `context`: Commit statuses `context`. The default value is `status-check`
- `description`: Commit statuses `description`. The default value is empty.

### Outputs

Nothing.
