# Build GitHub Action Release

This GitHub Action automates the process of creating a new GitHub Release for your GitHub Action repository. It generates a semantic version tag (with pre-release support), creates annotated tags, publishes release notes, and comments on the associated pull request. Designed for maintainers who want to release and version their actions efficiently in response to pull request merges.

---

## Features

- **Automated semantic versioning for your Action repository**
- **Supports creating pre-releases with a custom suffix**
- **Creates an annotated git tag for new versions**
- **Publishes a GitHub Release with version notes**
- **Comments the release status and tag on the pull request**
- **Issues a job summary showing the result of the release operation**

---

## Inputs

| Name                | Description                                                                                                                   | Required |
|---------------------|-------------------------------------------------------------------------------------------------------------------------------|----------|
| `prerelease`        | Mark the release as a pre-release (`true`/`false`).                                                                           | Yes      |
| `prerelease-suffix` | The suffix to append to the version number for pre-releases. For non-pre-releases, the version is based on the latest pre-release with this suffix. | Yes      |
| `token`             | The GitHub token used for authentication to create the release.                                                               | Yes      |

---

## Outputs

| Name              | Description                                                         |
|-------------------|---------------------------------------------------------------------|
| `version-tag`     | The new tag created for the release (e.g., `v1.0.0`).              |
| `version-number`  | The new tag without the "v" prefix (e.g., `1.0.0`).                |
| `build-status`    | The release outcome: Release Created, No Release, or Release Failed.|

---

## Usage

**Important:**

- This action must be run on a runner that supports PowerShell (Windows or Ubuntu).
- It is intended to be triggered by a pull request that has been closed and merged.
- The action requires a sufficiently scoped GitHub token to create tags and releases.

Add this composite action as a step in your workflow after your pull request has merged.

### Example Workflow

```yaml
name: Release GitHub Action

on:
  pull_request:
    types: [closed]

jobs:
  release-action:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v6

      - name: Create GitHub Action Release
        uses: lee-lott-actions/build-github-action-release@v1
        with:
          prerelease: 'false'
          prerelease-suffix: 'rc'
          token: ${{ secrets.GITHUB_TOKEN }}
```

---

## Notes

- The action will only proceed if the PR is merged.  
- Tags and releases follow [semantic versioning](https://semver.org/) and support pre-release naming.
- A summary of the release, including the release tag and URL, is added to the job summary.
- For pre-releases, an annotated tag is created but a GitHub Release is not published.
- For standard releases, an annotated tag is created and a GitHub Release is published with release notes and timing information.

---
