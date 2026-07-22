<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.2.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker action references a mutable image tag `:v2` instead of an immutable SHA digest. This means the image can be silently replaced with a different version, enabling supply-chain attacks. The failing reference is: `image: 'docker://ghcr.io/cbrgm/mastodon-github-action:v2'`. It should be replaced with a SHA digest, e.g. `image: 'ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:52`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside `run:` shell commands. In automerge.yml, multiple steps interpolate `${{ github.event.pull_request.html_url }}` directly into shell commands (`gh pr review --approve ${{ github.event.pull_request.html_url }}` and `gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`). A malicious PR title or URL could inject shell metacharacters. In tag.yml, the 'Publish Git Tag' step interpolates `${{ steps.bump-semver.outputs.new_version }}` directly into a shell variable assignment (`new_tag=${{ steps.bump-semver.outputs.new_version }}`), which is evaluated by the shell before quoting can protect it.

Locations:

- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:28`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:48`
- `.github/workflows/tag.yml:60`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` key. Without explicit permissions, workflows inherit the default repository token permissions (which may be broad). The affected files are `example-workflow.yml` and `example-workflow-envs.yml`.

Locations:

- `.github/workflows/example-workflow.yml:1`
- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. action.yml: Pinned Docker image from `docker://ghcr.io/cbrgm/mastodon-github-action:v2` to `docker://ghcr.io/cbrgm/mastodon-github-action:v2@sha256:b45d1329abcbea23b961085e7fbed7b2f91034772c9105b9b89c6da695154016`, preserving the docker:// scheme and :v2 tag inline. 2. automerge.yml: Moved all 5 `${{ github.event.pull_request.html_url }}` interpolations into `PR_URL` env vars and referenced as `"$PR_URL"` in shell commands across dependabot, renovate, and cbrgm jobs. 3. tag.yml: Moved `${{ steps.bump-semver.outputs.new_version }}` into a `NEW_VERSION` env var and referenced as `"$NEW_VERSION"` in the shell script. 4. example-workflow.yml and example-workflow-envs.yml: Added `permissions: {}` top-level blocks since these workflows only invoke the Mastodon action and require no GitHub token permissions.

