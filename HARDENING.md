<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.2.3** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image reference with a mutable tag instead of a SHA digest: `image: 'docker://ghcr.io/cbrgm/mastodon-github-action:v2'`. This is vulnerable to supply-chain attacks because the tag can be silently moved to point to a different image. Additionally, example-workflows/example-inline-message.yaml and example-workflows/example-multiline-message.yaml reference `cbrgm/mastodon-github-action@v2` (a mutable tag, not a 40-character commit SHA).

Locations:

- `action.yml:47`
- `example-workflows/example-inline-message.yaml:12`
- `example-workflows/example-multiline-message.yaml:12`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside `run:` shell command strings, allowing script injection. In .github/workflows/automerge.yml, `${{ github.event.pull_request.html_url }}` is interpolated directly into `gh pr review` and `gh pr merge` commands across 5 steps — an attacker-controlled PR URL could contain shell metacharacters. In .github/workflows/tag.yml, `${{ steps.bump-semver.outputs.new_version }}` is interpolated directly into a shell variable assignment (`new_tag=${{ steps.bump-semver.outputs.new_version }}`), which is then used unquoted in `git tag $new_tag` and `git push origin $new_tag`.

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:44`
- `.github/workflows/automerge.yml:52`
- `.github/workflows/tag.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all findings: (1) Pinned Docker image in action.yml to ghcr.io/cbrgm/mastodon-github-action:v2@sha256:31b4988083a47f567946b5a70f9b7a06f483073bce93d1c0c0dc5d0548665b47 preserving the docker:// scheme and :v2 tag inline. (2) Pinned cbrgm/mastodon-github-action@v2 to commit SHA 7fcc3650150fc062d707eeffb50e8f7a2704fd0f in both example workflow files. (3) Fixed script injection in automerge.yml across all 5 steps by moving github.event.pull_request.html_url into env: blocks as PR_URL and referencing it as "$PR_URL" in run: scripts. (4) Fixed script injection in tag.yml by moving steps.bump-semver.outputs.new_version into an env: block as NEW_VERSION and using "$new_tag" (double-quoted) throughout the shell script.

### Iteration 2

**Fixes applied:** github-env-injection, missing-permissions

**Notes:**

Fixed three findings: (1) In tag.yml 'Get Latest Tag' step, sanitized the attacker-controllable `latest_tag` value by piping through `printf '%s' "$latest_tag" | tr -d '\n\r'` before writing to $GITHUB_ENV, and also quoted the $GITHUB_ENV path. (2) Added `permissions: {}` to example-workflow.yml since the workflow only sends a Mastodon message via secrets and requires no GitHub token permissions. (3) Added `permissions: {}` to example-workflow-envs.yml for the same reason.

