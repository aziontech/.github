# Security Policy

This policy applies by default to repositories in the `aziontech` organization
that do not define their own `SECURITY.md`.

## Reporting a vulnerability

Please report security vulnerabilities responsibly and do not open a public issue
for them.

Preferred channel: use GitHub's private vulnerability reporting on the affected
repository ("Security" tab, "Report a vulnerability"). If that is not available,
contact the Security Office through the organization's designated security contact.

> Maintainers: confirm and pin the security contact channel for this line before
> relying on it.

When reporting, please include, when possible:

- A description of the issue and its potential impact.
- Steps to reproduce, or a minimal proof of concept.
- Affected repository, version, commit or environment.
- Any suggested remediation you may have.

Please do not include real secrets, credentials or production data in a report.
Redact them and describe the class of the exposure instead.

## What to expect

- Acknowledgement of the report as soon as it is triaged.
- An assessment of severity and an initial response with next steps.
- Coordination on a fix and a disclosure timeline proportional to severity.

## Scope

This default policy covers source code and configuration hosted in this
organization. Findings in third party dependencies should be reported upstream
and, when they affect us, also through the channel above.

## Good practices we ask of everyone

- Never commit secrets, private keys, credentials or `.env` files. Use the
  approved secret management instead.
- Prefer least privilege for tokens, workflow permissions and access grants.
- Keep dependencies current and address high severity advisories promptly.
