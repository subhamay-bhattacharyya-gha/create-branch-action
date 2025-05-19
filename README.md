# Create Feature Branch Composite Action

![Release](https://github.com/subhamay-bhattacharyya-gha/create-branch-action/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Open Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Monthly Commit Activity](https://img.shields.io/github/commit-activity/m/subhamay-bhattacharyya-gha/create-branch-action)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/ad585d14ed06ff22b281ef1453d1a0ab/raw/create-branch-action.json?)&nbsp;[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Available-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/create-feature-branch)

A GitHub Composite Action to automatically create a feature branch from an issue using the GitHub API.

```mermaid
sequenceDiagram
    participant User
    participant GitHub
    participant Workflow
    participant Developer

    User->>GitHub: Create issue
    GitHub->>Developer: Assign issue
    GitHub->>Workflow: Trigger workflow

    alt Issue type = bug
        Workflow->>GitHub: Create branch "bug/<issue-number>"
    else Issue type = task or sub-task or feature
        Workflow->>GitHub: Create branch "feature/<issue-number>"
    else Invalid issue type
        Workflow->>GitHub: Fail the workflow and print an error in the summary
    end

    Developer->>GitHub: Unassign issue
    GitHub->>NewDeveloper: Assign issue
    GitHub->>Workflow: Trigger workflow

    alt Valid issue type
        Workflow->>GitHub: Create new branch with suffix (e.g., "bug/<issue-number>-AB12")
    else Invalid issue type
        Workflow->>GitHub: Fail the workflow and print an error in the summary
    end
```

## Prerequisite

The repository name must follow the pattern:

```text
<Numeric Project Sequence>-<Project Code>-<Short Description>
```

**Example:** `001-PORTAL-dark-mode-support`

## Features

- Creates a new branch based on an existing GitHub issue.
- Builds the branch name using project metadata and a truncated, slugified issue title.
- Posts a comment on the issue with a link to the new branch.
- Branch naming format:

  ```text
  <Issue Type>/<Project Code>-<Project Sequence>.<Issue Number>-<Truncated Slugified Title>
  ```

## Inputs

| Name           | Description                                       | Required | Default |
|----------------|---------------------------------------------------|----------|---------|
| `base-branch`  | The base branch to create the new branch from.    | No       | `main`  |
| `title-length` | Number of characters to keep from issue title.    | No       | `15`    |
| `github-token` | GitHub token with repo access.                    | Yes      | –       |
| `dry-run`      | Simulate the process only without creating branch.| Yes      | `false` |

## Sample Usage

```yaml
on:
  issues:
    types: [opened, assigned]

jobs:
  create-feature-branch:
    runs-on: ubuntu-latest
    permissions:
      issues: write
      contents: write
    steps:
      - uses: subhamay-bhattacharyya-gha/create-branch-action@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          title-length: 20
```

> **Note:** This action is triggered by issue events. Be sure your workflow listens to events like `opened` or `assigned`.

## How It Works

1. Extracts the issue number and title.
2. Builds the branch name in this format:

   ```text
   <Issue Type>/<Project Code>-<Project Sequence>.<Issue Number>-<Truncated Slugified Title>
   ```

3. Uses the GitHub API to create the new branch from the base branch.
4. Comments on the issue with a link to the new branch.

## Example Output

**Given:**

- Repository: `001-PORTAL-dark-mode-support`
- Issue: #123 titled `Add support for dark mode`

**Generated branch:**

```text
feature/PORTAL-001.123-add-support
```

## Requirements

- `jq` (used internally for parsing JSON)
- A GitHub token with `repo` and `issues` permissions

## License

MIT
