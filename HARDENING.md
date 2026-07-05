<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action--/v2.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--mastodon-github-action--/v2.2.2** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a mutable Docker image tag 'docker://ghcr.io/cbrgm/mastodon-github-action:v2' in runs.image instead of a SHA digest. This is vulnerable to supply-chain attacks as the tag can be silently updated to point to a different image.

Locations:

- `action.yml:48`

### script-injection (severity: high)

automerge.yml directly interpolates ${{ github.event.pull_request.html_url }} inside run: shell commands (rule a). The value flows through YAML template substitution before the shell sees it, bypassing shell quoting. Affected steps: 'Approve request' (dependabot job, line 24), 'Enable automerge' (dependabot job, line 29), 'Approve request' (renovate job, line 36), 'Enable automerge' (renovate job, line 41), 'Enable automerge' (cbrgm job, line 49).

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:49`

### script-injection (severity: high)

tag.yml directly interpolates ${{ steps.bump-semver.outputs.new_version }} inside a run: shell command (rule a). The step output is assigned to a shell variable via YAML template substitution: `new_tag=${{ steps.bump-semver.outputs.new_version }}`, which means the value is embedded in the script before the shell parses it, allowing shell metacharacters to be injected.

Locations:

- `.github/workflows/tag.yml:57`

### permissions (severity: medium)

example-workflow.yml has no top-level permissions: key and no job-level permissions: key on any job. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/example-workflow.yml:1`

### permissions (severity: medium)

example-workflow-envs.yml has no top-level permissions: key and no job-level permissions: key on any job. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, permissions

**Notes:**

Fixed 5 findings: (1) Pinned ghcr.io/cbrgm/mastodon-github-action:v2 to SHA digest sha256:b45d1329abcbea23b961085e7fbed7b2f91034772c9105b9b89c6da695154016 in action.yml. (2) Fixed script injection in automerge.yml by moving github.event.pull_request.html_url into PR_URL env var for all 5 affected steps (dependabot approve, dependabot automerge, renovate approve, renovate automerge, cbrgm automerge). (3) Fixed script injection in tag.yml by moving steps.bump-semver.outputs.new_version into NEW_VERSION env var. (4) Added permissions: {} to example-workflow.yml. (5) Added permissions: {} to example-workflow-envs.yml.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in .github/workflows/tag.yml at line 43. The `latest_tag` value (derived from repository-controlled git tag data) is now sanitized with `printf '%s' "$latest_tag" | tr -d '\n\r'` before being written to $GITHUB_ENV. The sanitized value is stored in `safe_tag` and written as `echo "latest_tag=$safe_tag" >> "$GITHUB_ENV"` (also added quotes around $GITHUB_ENV for best practice).

