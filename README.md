# Bump digest for chainguard image

Chainguard only allows the `:latest` and `:latest-dev` tags for their images ([announcement](https://www.chainguard.dev/unchained/a-guide-on-how-to-use-chainguard-images-for-public-catalog-tier-users)). Older verions must be referenced by digests (`@sha256:...`). Use this action to setup nightly github actions that bump chainguard images to the newest digests.

## use the action

example for nightly job:

```yaml
name: Bump redis image

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

permissions:
  contents: write

jobs:
  bump-chainguard-images:
    runs-on: ubuntu-latest
    steps:
      - name: bump redis image
        uses: digitalservicebund/github-actions/bump-chainguard-digest@961e1cd525f8af56785a024bafc2b3b6a2f7449a
        with:
          image_name: redis
          file_path: manifests/base/redis-deployment.yaml
          github_token: ${{ github.token }}
```

### inputs:

| input          | info                                                                                                                                    |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `image_name`   | name of the image to check for (e.g. `nginx`)                                                                                           |
| `file_path`    | path to the file where the image is located (e.g `frontend/Dockerfile`)                                                                 |
| `github_token` | required for using the github api to make commits (steps inside composite github actions cannot directly access the `secrets` context). |

> **Note**
>
> Triggering another github workflow (e.g. a build pipeline) does not work automatically via the [push trigger](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#push). You'll need to trigger the workflow manually ([e.g. via the github cli](https://github.com/digitalservicebund/ris-backend-service/blob/141a80325c271fa0a6f36ca5a6a03618fe110142/.github/workflows/bump_images.yml#L28))
