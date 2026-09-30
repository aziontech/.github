# Contributing

Thanks for contributing to an `aziontech` project. This guide applies to every repository in the organization that
does not publish its own `CONTRIBUTING.md`.

## Before you start

- **Security issues:** do not open an issue or pull request. Follow the [Security Policy](SECURITY.md).
- **Bugs and features:** search the existing issues first, then open one with the matching template.
- **Large changes:** open an issue to discuss the approach before writing code.

## Pull requests

1. Branch from the default branch (`main`). Keep branches short-lived.
2. Keep each pull request focused on one change.
3. Title in [Conventional Commits](https://www.conventionalcommits.org/) format. A ticket prefix is optional:
   - `fix: handle empty response`
   - `[ENG-123] feat(cache): add purge by tag`

   Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
4. Add or update tests for the behavior you change, and make sure CI is green.
5. Fill in the pull request template, including the version bump (`#major`, `#minor`, `#patch` or `#none`).
6. Do not commit secrets, credentials or internal hostnames. Secret scanning blocks pushes that contain them.

Pull requests are merged after review by the code owners of the changed files.

## Versioning

Releases follow [Semantic Versioning](https://semver.org/) and are tagged `vMAJOR.MINOR.PATCH`. Notable changes
are recorded in the repository's `CHANGELOG.md`.
