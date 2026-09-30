# Contributing

These are the default contribution guidelines for the `aziontech` organization.
A repository may define its own `CONTRIBUTING.md`, which takes precedence.

## Pull requests

- Keep pull requests focused and reasonably small.
- Write a clear title and description. Follow Conventional Commits in the title
  (for example `feat:`, `fix:`, `chore:`), and reference the related issue.
- Make sure the required checks pass before requesting a merge.
- Request review from the appropriate code owners.

## Commits

- Use clear, imperative commit messages.
- Do not commit generated artifacts, large binaries, secrets or credentials.

## Security and secrets

- Never commit secrets, private keys, credentials or environment files.
- If you believe you committed a secret, treat it as exposed: rotate it and
  remove it from history. Removing the line in a later commit is not enough.
- Running a pre-commit hook locally is strongly recommended to catch secrets and
  common issues before they leave your machine.

## Quality

- Add or update tests when you change behavior.
- Keep code style consistent with the surrounding code and the repository's
  linters.
