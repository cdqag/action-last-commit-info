# Last Commit Info

This GitHub Action retrieves comprehensive information about the last commit in a repository.
It can fetch commit details for the entire repository or for a specific file path, making it useful for various CI/CD workflows that need to access commit metadata.

## Features

- Get the last commit SHA, message, and metadata
- Support for filtering by specific file path
- Retrieve both author and committer information
- Get API and HTML URLs for the commit
- Works with any repository (default: current repository)
- Uses GitHub CLI for reliable API access - It DOES NOT require checkout of the repository

## Inputs

| Name | Required | Default | Description |
|-------|----------|---------|-------------|
| `github-token` | No | `${{ github.token }}` | GitHub token for authentication |
| `repository` | No | `${{ github.repository }}` | Repository to check for the last commit (format: `owner/repo`) |
| `path` | No | `""` (entire repository) | Specific file path to check for last commit. When specified, returns the last commit that modified this specific path |

## Outputs

| Name | Description |
|--------|-------------|
| `sha` | The SHA hash of the last commit |
| `message` | The commit message of the last commit |
| `author-name` | The name of the commit author |
| `author-email` | The email address of the commit author |
| `committer-name` | The name of the commit committer |
| `committer-email` | The email address of the commit committer |
| `api-url` | The GitHub API URL for the commit |
| `html-url` | The GitHub web URL for the commit |

## Example Usage

### Basic Usage - Get Last Commit Info

```yaml
name: Get Last Commit

on: [push, pull_request]

jobs:
  commit-info:
    runs-on: ubuntu-latest
    steps:
      - name: Get last commit info
        id: commit
        uses: cdqag/action-last-commit-info@v1

      - name: Display commit info
        run: |
          echo "Last commit SHA: ${{ steps.commit.outputs.sha }}"
          echo "Commit message: ${{ steps.commit.outputs.message }}"
          echo "Author: ${{ steps.commit.outputs.author-name }} <${{ steps.commit.outputs.author-email }}>"
          echo "Commit URL: ${{ steps.commit.outputs.html-url }}"
```

### Check Last Commit for Specific File

```yaml
name: Check Dockerfile Changes

on: [push, pull_request]

jobs:
  dockerfile-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Get last commit for Dockerfile
        id: dockerfile-commit
        uses: cdqag/action-last-commit-info@v1
        with:
          path: Dockerfile

      - name: Check if Dockerfile was recently modified
        run: |
          echo "Dockerfile was last modified in commit: ${{ steps.dockerfile-commit.outputs.sha }}"
          echo "Commit message: ${{ steps.dockerfile-commit.outputs.message }}"
```

### Cross-Repository Usage

```yaml
name: Check External Repo

on: workflow_dispatch

jobs:
  external-check:
    runs-on: ubuntu-latest
    steps:
      - name: Get commit from another repo
        id: external-commit
        uses: cdqag/action-last-commit-info@v1
        with:
          repository: octocat/Hello-World
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Display external repo info
        run: |
          echo "Last commit in Hello-World: ${{ steps.external-commit.outputs.sha }}"
          echo "Message: ${{ steps.external-commit.outputs.message }}"
```

## License

This project is licensed under the Apache-2.0 License. See the [LICENSE](LICENSE) file for details.
