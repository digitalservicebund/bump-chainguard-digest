# Bump digest for chainguard image

Chainguard only allows the `:latest` and `:latest-dev` tags for their images ([announcement](https://www.chainguard.dev/unchained/a-guide-on-how-to-use-chainguard-images-for-public-catalog-tier-users)). Older verions must be referenced by digests (`@sha256:...`). Use this action to setup nightly github actions that bump chainguard images to the newest digests.

## Changes since v1

This action now creates a pull request instead of pushing directly to the branch for compatibility with branch protection rules.
If not disabled (via input `auto_merge`, see below), it tries to (auto-)merge the PR.

1. Enable auto merge for your repository
2. Add `pull-requests: write` permissions (see example below)

See [GitHub docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request) for details.

## Usage

Example for nightly job:

```yaml
name: Bump redis image

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  bump-chainguard-images:
    runs-on: ubuntu-latest
    steps:
      - name: bump redis image
        uses: digitalservicebund/bump-chainguard-digest@LATEST_HASH
        with:
          image_name: redis
          file_path: manifests/base/redis-deployment.yaml
          github_token: ${{ github.token }}
```

### Inputs

| input          | info                                                                                                                                    |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `image_name`   | name of the image to check for (e.g. `nginx`)                                                                                           |
| `file_path`    | path to the file where the image is located (e.g `frontend/Dockerfile`)                                                                 |
| `auto_merge`   | whether to auto-merge the PR created (defaults to true)                                                                                 |
| `github_token` | required for using the github api to make commits (steps inside composite github actions cannot directly access the `secrets` context). |
