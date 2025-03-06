# update-required-status-check-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/update-required-status-check-action/main/LICENSE) | [action.yaml](action.yaml)

GitHub Action To Update GitHub's Required Status Check

## How To Use

Run this action in a `workflow_run` workflow.

```yaml
name: Status Check
on:
  workflow_run:
    workflows: [test] # PLEASE FIX THIS
    types:
      - completed
jobs:
  status-check:
    runs-on: ubuntu-24.04
    permissions:
      statuses: write # To update commit statuses
    steps:
      - uses: suzuki-shunsuke/update-required-status-check-action@main
```

## Inputs / Outputs

Please see [action.yaml](action.yaml) too.

All inputs are optional.

- github_token
- context
- description
