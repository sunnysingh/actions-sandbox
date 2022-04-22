# GitHub Actions Sandbox

This is test environment for [GitHub Actions](https://github.com/features/actions).

## Purpose

[GitHub Actions](https://github.com/features/actions) is a great tool to manage continuous integration and deployment (CI/CD), but there are some undocumented edge cases worth knowing about.

The purpose of this `actions-sandbox` repository is to provide a place to test out CI/CD workflows and setups with GitHub Actions.

## Problem #1: Auto Merging Pull Requests (PRs)

If you want to automatically create and merge PRs via some CI/CD process, then you will want to figure out how to automatically create those PRs, automatically approve them, and automatically merge them in a way so that existing workflows are still triggered (e.g. a deployment when a PR is merged to the main branch).

This repository has the following workflows to demonstrate this:

1. [test.yml](.github/workflows/test.yml) - a workflow to block PR merges until it passes
2. [auto-approve.yml](.github/workflows/auto-approve.yml) - a workflow to automatically approve PRs with the `automated` label
3. [deploy-main.yml](.github/workflows/deploy-main.yml) - a workflow which triggers on merges to the `main` branch

With these workflows in place, you can create a PR with the [GitHub CLI](https://cli.github.com/):

```sh
gh pr create --fill --label automated
```

You can then configure the PR to be automatically merged once all checks pass:

```sh
gh pr merge $(gh pr view --json number --jq .number) --auto --rebase
```

For automatic merges using the `--auto` flag, you will also need to [enable auto-merge in your repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request#enabling-auto-merge).

The GitHub CLI is [automatically installed in GitHub-hosted runners](https://docs.github.com/en/actions/using-workflows/using-github-cli-in-workflows), which enables you to automatically create PRs via GitHub Actions. Make sure to authenticate via a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) to avoid breaking existing workflows.
