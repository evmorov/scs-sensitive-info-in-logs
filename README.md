# Sensitive Credential in Action Logs

This lab demonstrates how unsafe diagnostic logging in a third-party action can
expose an ephemeral workflow credential.

## Vulnerability1

`pull_request_target` runs the notification workflow in the base repository
context. It passes a write-capable `GITHUB_TOKEN` and an attacker-controlled
pull request title to an intentionally vulnerable action.

The action treats the title as an issue number. A non-numeric title causes an
API error, and its error handler writes a hex-encoded copy of the credential
to the job log. The leaked token has `actions: write`, so it can dispatch the
trusted reward workflow while the notification job remains active.

## Setup

1. Create a repository secret named `FLAG`.
2. Ensure `dcodemy/scs-secret-action` is accessible to repositories in the
   `dcodemy` organization.
3. Allow students to fork this private repository and open pull requests.
4. Use a separate repository and flag per student. Reward releases are visible
   to everyone with repository read access.

## Exercise

Fork the repository and determine how attacker-controlled pull request metadata
can force the notification action into an unsafe diagnostic path. Use the
resulting short-lived credential exposure to reach the trusted reward workflow
before the notification job ends.

## Defenses

- Never serialize complete API errors or request metadata into logs.
- Never log credentials, including encoded or transformed forms.
- Pin external actions to reviewed commit SHAs.
- Give notification workflows only the permissions they require.
- Do not combine untrusted pull request metadata with privileged workflows.
