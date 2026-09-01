<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.2.4** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml `runs.image:` field references a mutable Docker image tag (`docker://ghcr.io/cbrgm/mastodon-github-action:v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image on future runs. It should be pinned to a SHA digest, e.g. `ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:47`

### script-injection (severity: high)

Rule (a): Five `run:` blocks in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. Although the value is a URL, any GitHub Actions expression interpolated directly into a `run:` block is a script-injection risk — the value is substituted by the template engine before the shell ever sees it, bypassing shell quoting. Offending lines:
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot job, line 24)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot job, line 29)
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (renovate job, line 37)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (renovate job, line 42)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (cbrgm job, line 52)
Fix: move the URL into an `env:` variable and reference it as a quoted shell variable, e.g. `env: PR_URL: ${{ github.event.pull_request.html_url }}` then `run: gh pr review --approve "$PR_URL"`.

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:37`
- `.github/workflows/automerge.yml:42`
- `.github/workflows/automerge.yml:52`

### script-injection (severity: high)

Rule (a): The 'Publish Git Tag' `run:` block in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into the shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The `steps.*.outputs.*` context is a workflow-controllable value that is substituted by the template engine before the shell executes, allowing shell metacharacters to be injected. Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: NEW_TAG: ${{ steps.bump-semver.outputs.new_version }}` then `new_tag="$NEW_TAG"`.

Locations:

- `.github/workflows/tag.yml:57`

### missing-permissions (severity: medium)

The workflow file example-workflow.yml has no top-level `permissions:` key and the single job `build` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Add a `permissions:` block with the minimum required scopes (e.g. `permissions: {}` if no GitHub token access is needed).

Locations:

- `.github/workflows/example-workflow.yml:1`

### missing-permissions (severity: medium)

The workflow file example-workflow-envs.yml has no top-level `permissions:` key and the single job `build` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Add a `permissions:` block with the minimum required scopes (e.g. `permissions: {}` if no GitHub token access is needed).

Locations:

- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed 5 findings across 5 files:
1. action.yml: Pinned Docker image `ghcr.io/cbrgm/mastodon-github-action:v2` to immutable digest `sha256:2841dac3ec7e04272f7adb763b115f3ec5129642090a0254af06669ad62f30d8`, preserving the `docker://` scheme and `:v2` tag.
2. .github/workflows/automerge.yml: Moved all 5 instances of `${{ github.event.pull_request.html_url }}` out of `run:` blocks into `env:` blocks as `PR_URL`, then referenced as `"$PR_URL"` in shell commands.
3. .github/workflows/tag.yml: Moved `${{ steps.bump-semver.outputs.new_version }}` out of the `run:` block into an `env:` block as `NEW_VERSION`, then referenced as `"$NEW_VERSION"` in the shell script.
4. .github/workflows/example-workflow.yml: Added `permissions: {}` top-level block.
5. .github/workflows/example-workflow-envs.yml: Added `permissions: {}` top-level block.

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansions in the 'Publish Git Tag' step of .github/workflows/tag.yml. Changed `git tag $new_tag` to `git tag "$new_tag"` and `git push origin $new_tag` to `git push origin "$new_tag"`. Also double-quoted `$latest_tag` in the `git rev-list` command for defense-in-depth. The workflow-controllable value was already correctly isolated in the `env:` block via `NEW_VERSION: ${{ steps.bump-semver.outputs.new_version }}`.

