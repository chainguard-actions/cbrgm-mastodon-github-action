<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.2.2** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image reference with a mutable tag ('docker://ghcr.io/cbrgm/mastodon-github-action:v2') instead of an immutable SHA digest. This means the action can silently pull a different (potentially malicious) image on each run. The image reference should be pinned to a SHA digest, e.g. 'docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:57`

### script-injection (severity: high)

automerge.yml directly interpolates ${{ github.event.pull_request.html_url }} inside run: shell commands (sub-rule a). This value is attacker-controlled — a malicious PR title or URL could inject arbitrary shell commands. Affected steps: 'Approve request' (dependabot job, line 24), 'Enable automerge' (dependabot job, line 30), 'Approve request' (renovate job, line 36), 'Enable automerge' (renovate job, line 42), 'Enable automerge' (cbrgm job, line 50). The fix is to pass the URL via an env: variable and reference it as a quoted shell variable (e.g. "$PR_URL").

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:42`
- `.github/workflows/automerge.yml:50`

### script-injection (severity: high)

tag.yml directly interpolates ${{ steps.bump-semver.outputs.new_version }} inside a run: shell command (sub-rule a): 'new_tag=${{ steps.bump-semver.outputs.new_version }}'. Step outputs flow through YAML template substitution before the shell sees them, so a crafted output value could inject arbitrary shell commands. The fix is to pass the value via an env: variable and reference it as a quoted shell variable (e.g. NEW_TAG="$NEW_VERSION").

Locations:

- `.github/workflows/tag.yml:55`

### missing-permissions (severity: medium)

example-workflow.yml and example-workflow-envs.yml have no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, workflows inherit the repository's default token permissions, which may be overly broad. A top-level 'permissions: {}' or minimal specific scopes should be added.

Locations:

- `.github/workflows/example-workflow.yml:1`
- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed 4 findings: (1) Pinned Docker image in action.yml to immutable SHA digest sha256:b45d1329abcbea23b961085e7fbed7b2f91034772c9105b9b89c6da695154016 while preserving the docker:// scheme and :v2 tag. (2) Fixed 5 script-injection instances in automerge.yml by moving github.event.pull_request.html_url into env: blocks as PR_URL and referencing as "$PR_URL" in shell commands. (3) Fixed script-injection in tag.yml by moving steps.bump-semver.outputs.new_version into an env: block as NEW_VERSION and referencing as "$NEW_VERSION" in the shell script. (4) Added 'permissions: {}' to example-workflow.yml and example-workflow-envs.yml to enforce least-privilege token access.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `cbrgm/mastodon-github-action@v2` to `cbrgm/mastodon-github-action@ac2d8e8c9986a17b824dd12dd9df4ce5fcd813c1 # v2` in both example workflow files:
- example-workflows/example-inline-message.yaml (line 11)
- example-workflows/example-multiline-message.yaml (line 11)

The SHA was resolved via git ls-remote. The original `v2` tag is preserved as a comment for human readability.

