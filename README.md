# devops-engineering-ci-public-extract-version-action

Reusable composite GitHub Action for normalizing version and tagging information from a Git ref before a build starts.

## Overview

This action is typically the first reusable component in the build chain. It inspects the triggering Git ref, determines the release strategy, queries the registry when needed, and produces the version and tag outputs that downstream workflows use for image publication and optional redeploy behavior.

For inbound and outbound dependency relationships, see [docs/WHO-CALLS-WHAT.md](docs/WHO-CALLS-WHAT.md).

## Action

### `action.yml`

Key responsibilities:

- Detects the ref strategy for final releases, pre-releases, patch release candidates, and patch branches.
- Extracts a normalized primary tag.
- Determines whether the build should be treated as a release.
- Queries ACR to discover the latest semantic version already published.
- Decides whether additional tags such as `latest` or `pre-release` should be emitted.
- Generates a unique `build_id` and a shortened version used for language pack lookup.
- Emits the consolidated tag list for downstream retagging.

## Supported tagging strategies

The current action logic recognizes these patterns:

- `vX.Y.Z.final`: final release, emitted as `X.Y.Z`
- `vX.Y.alphaN`, `vX.Y.betaN`, `vX.Y.rcN`: pre-release, emitted as the normalized pre-release version
- `vX.Y.Z.rcN`: patch release candidate
- `patch/vNN/TICKET-123`: patch branch build, emitted as a compound version

## Important inputs

- `ref_name`: the git ref name to inspect.
- `acr`: registry host used for version comparisons.
- `module` or `image_reference`: image path used when checking current published versions.
- `registry_username` and `registry_password`: credentials for registry inspection.
- `engineering_prefix`: registry prefix used when resolving image paths.

## Outputs

- `build_id`: unique build identifier derived from the tag, date, and run number.
- `is_release`: whether the build is treated as a release.
- `major_version` and `minor_version`: parsed semantic version components.
- `shortened_version`: compact version string used for language pack lookup.
- `tag`: primary normalized version tag.
- `tags`: full comma-separated list of tags to apply downstream.
- `namespace`: optional namespace suffix used by downstream redeploy logic.

## How this repo fits the wider build stack

- Upstream: it runs directly in the caller workflow that was triggered by a tag or branch.
- Downstream: its outputs are passed into the platform, module, and independent-module reusable workflows.
- Scope: this repo owns version interpretation and downstream tag generation, not builds or deployment.
