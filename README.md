# actions-verify-npm-package-bump
## Description
Verifies that the version in package.json does not match the latest tag.
If it does not, then we proceed with a release.

## Requirements
- [Checkout](https://github.com/actions/checkout) - to clone the repo. Needs a `fetch_depth` of 0

## Environment Variables/Secrets

| Env Var/Secret | Desription | Required |
|-------|------------|----------|
| NPM_RELEASE_TOKEN | Secret/Env Var containing the NPM token used for releases. | true |

## Usage
```yaml
name: Release
on: [ main ]
jobs:
  verify_release:
    name: Verify Release
    runs-on: ubuntu-latest
    outputs:
      release: ${{ steps.actions-verify-npm-package-bump.outputs.release }}
    steps:
      - uses: actions/checkout@v4
        with:
          fetch_depth: 0

      - uses: actions-verify-npm-package-bump@main
  release:
    name: Release UI Component
    needs: verify_release
    if: needs.verify_release.outputs.release == 'true'
    steps:
      - uses: actions/checkout@v4

      - uses: prefecthq/actions-release-ui-components@main
        with:
          NPM_TOKEN: ${{ secrets.PREFECT_UI_COMPONENTS_NPM_TOKEN }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NEBULA_UI_ACTIONS_RW_TOKEN: ${{ secrets.NEBULA_UI_ACTIONS_RW }}
          PREFECT_OSS_ACTIONS_RW_TOKEN: ${{ secrets.PREFECT_ACTIONS_RW }}
```