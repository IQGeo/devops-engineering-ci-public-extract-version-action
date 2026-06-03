# Who Calls What

## Scope

This document shows the inbound and outbound dependency relationships for `devops-engineering-ci-public-extract-version-action`.

## Matrix

| Direction | Component | Relationship | Notes |
| --- | --- | --- | --- |
| Called by | Caller workflow triggered by a tag, branch, or manual dispatch | Inbound | Typically runs before any reusable build workflow is invoked |
| Supplies outputs to | `devops-engineering-ci-public-build-platform-workflow` | Downstream | Provides normalized version, tags, build id, and release intent |
| Supplies outputs to | `devops-engineering-ci-public-build-module-workflow` | Downstream | Provides normalized version, tags, build id, and release intent |
| Supplies outputs to | `devops-engineering-ci-public-build-independent-module-workflow` | Downstream | Can supply normalized version data when the caller chooses to use it |
| Uses | ACR registry metadata | Internal | Queries the registry to compare versions and determine `latest` or `pre-release` tagging |

## Notes

- This action is usually the first reusable component in the build stack.
- It does not call other workspace workflows, but its outputs shape the behavior of downstream workflows.