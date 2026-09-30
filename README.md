# aziontech/.github

Default community health files for the `aziontech` GitHub organization.

GitHub uses the files in this repository for every `aziontech` repository that does not have its own version.
A file in a repository always takes precedence over the default here.

| File | Default for |
|---|---|
| [`SECURITY.md`](SECURITY.md) | Vulnerability disclosure (security@azion.com) |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Branches, Conventional Commits, pull requests, versioning |
| [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE/) | Bug report and feature request forms; blank issues disabled |
| [`.github/pull_request_template.md`](.github/pull_request_template.md) | Pull request description |

`CODEOWNERS` is **not** inherited by other repositories: each repository keeps its own. The `CODEOWNERS` here only
covers this repository.

This repository is public by GitHub requirement. It must not contain internal policies, procedures, hostnames or
contacts beyond the public security address.

## Changing a default

Open a pull request. Changes here affect every repository in the organization that relies on the default, so they
need review from Delivery Engineering, and from Security Office for anything security-related (see `CODEOWNERS`).
