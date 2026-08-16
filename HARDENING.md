<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.26

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.1.26** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In automerge.yml, ${{ github.event.pull_request.html_url }} is interpolated directly into shell commands across five steps in three jobs (dependabot, renovate, cbrgm). An attacker who can control the PR URL could inject shell metacharacters. In tag.yml, ${{ steps.bump-semver.outputs.new_version }} is interpolated directly into a run: block, allowing a compromised or malicious action step to inject shell commands.

Locations:

- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:28`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:48`
- `.github/workflows/tag.yml:55`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable branch refs (@main) rather than full 40-character commit SHAs, and action.yml references a Docker image by mutable tag (:v2) instead of a SHA digest. Affected references: (1) action.yml — `image: docker://ghcr.io/cbrgm/mastodon-github-action:v2` (tag, not SHA digest); (2) .github/workflows/stale.yml — `cbrgm/cleanup-stale-branches-action@main`; (3) .github/workflows/tag.yml — `cbrgm/semver-bump-action@main`; (4) .github/workflows/example-workflow.yml — `cbrgm/mastodon-github-action@main`; (5) .github/workflows/example-workflow-envs.yml — `cbrgm/mastodon-github-action@main`.

Locations:

- `action.yml:44`
- `.github/workflows/stale.yml:33`
- `.github/workflows/tag.yml:49`
- `.github/workflows/example-workflow.yml:10`
- `.github/workflows/example-workflow-envs.yml:10`

### missing-permissions (severity: medium)

Two workflow files have no top-level permissions: key and no job-level permissions: key on any of their jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Affected files: example-workflow.yml and example-workflow-envs.yml.

Locations:

- `.github/workflows/example-workflow.yml:1`
- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings:

1. script-injection (automerge.yml lines 23,28,36,41,48): Moved all ${{ github.event.pull_request.html_url }} interpolations out of run: blocks into env: blocks as PR_URL, then referenced as "$PR_URL" in shell commands.

2. script-injection (tag.yml line 55): Moved ${{ steps.bump-semver.outputs.new_version }} into an env: block as NEW_VERSION, then referenced as "$NEW_VERSION" in the shell script.

3. unpinned-uses (action.yml line 44): Pinned docker://ghcr.io/cbrgm/mastodon-github-action:v2 to digest sha256:b45d1329abcbea23b961085e7fbed7b2f91034772c9105b9b89c6da695154016.

4. unpinned-uses (stale.yml line 33): Pinned cbrgm/cleanup-stale-branches-action@main to @1cd2068354f38284bb05b8ba279ae30790d68c44 # main.

5. unpinned-uses (tag.yml line 49): Pinned cbrgm/semver-bump-action@main to @cc89dae95968de9a49b9a4879290be60e1dd5600 # main.

6. unpinned-uses (example-workflow.yml line 10): Pinned cbrgm/mastodon-github-action@main to @3ecf31935bd0ef42511601e24d8b676da3c1ce13 # main.

7. unpinned-uses (example-workflow-envs.yml line 10): Pinned cbrgm/mastodon-github-action@main to @3ecf31935bd0ef42511601e24d8b676da3c1ce13 # main.

8. missing-permissions (example-workflow.yml): Added top-level `permissions: {}` block.

9. missing-permissions (example-workflow-envs.yml): Added top-level `permissions: {}` block.

