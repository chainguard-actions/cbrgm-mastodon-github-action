<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.27

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.1.27** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image referenced by a mutable tag rather than a SHA digest. `image: 'docker://ghcr.io/cbrgm/mastodon-github-action:v2'` uses the tag `:v2`, which can be silently replaced by a malicious image. It should be pinned to a specific SHA256 digest, e.g. `docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:44`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` steps in automerge.yml directly interpolate GitHub Actions expressions into shell commands. The value `${{ github.event.pull_request.html_url }}` is expanded by the template engine before the shell sees it, allowing an attacker to inject arbitrary shell metacharacters via a crafted PR URL. Offending lines: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` and `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`. These should be passed via an `env:` variable and double-quoted in the shell.

Locations:

- `.github/workflows/automerge.yml:20`
- `.github/workflows/automerge.yml:26`
- `.github/workflows/automerge.yml:35`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:50`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' step in tag.yml directly interpolates a step output expression into a shell `run:` block: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The expression is expanded by the template engine before the shell executes the script, allowing injection of arbitrary shell metacharacters if the step output is attacker-influenced. The value should be passed via an `env:` variable and double-quoted.

Locations:

- `.github/workflows/tag.yml:57`

### missing-permissions (severity: medium)

The workflow file example-workflow.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions:` block should be added.

Locations:

- `.github/workflows/example-workflow.yml:1`

### missing-permissions (severity: medium)

The workflow file example-workflow-envs.yml has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions:` block should be added.

Locations:

- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all 5 findings: (1) Pinned Docker image in action.yml from mutable tag :v2 to digest sha256:b45d1329abcbea23b961085e7fbed7b2f91034772c9105b9b89c6da695154016, preserving docker:// scheme and tag inline. (2) Fixed 5 script-injection occurrences in automerge.yml by moving github.event.pull_request.html_url into env: blocks as PR_URL and double-quoting in shell. (3) Fixed script-injection in tag.yml by moving steps.bump-semver.outputs.new_version into an env: block as NEW_VERSION and double-quoting in shell. (4) Added permissions: {} to example-workflow.yml. (5) Added permissions: {} to example-workflow-envs.yml.

