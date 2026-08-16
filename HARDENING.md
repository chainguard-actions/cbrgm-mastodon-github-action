<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.25

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.1.25** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and action.yml reference actions or Docker images by mutable tags/branches instead of full 40-character commit SHAs or SHA digests.

- action.yml: `image: 'docker://ghcr.io/cbrgm/mastodon-github-action:v2'` — mutable tag, not a SHA digest
- .github/workflows/go-binaries.yml: `uses: cbrgm/pgp-sign-artifact-action@v1` and `uses: cbrgm/semver-tag-sync-action@v1`
- .github/workflows/tag.yml: `uses: cbrgm/semver-bump-action@main`
- .github/workflows/stale.yml: `uses: cbrgm/cleanup-stale-branches-action@main`
- .github/workflows/example-workflow.yml: `uses: cbrgm/mastodon-github-action@main`
- .github/workflows/example-workflow-envs.yml: `uses: cbrgm/mastodon-github-action@main`

Locations:

- `action.yml:43`
- `.github/workflows/go-binaries.yml:33`
- `.github/workflows/go-binaries.yml:44`
- `.github/workflows/tag.yml:40`
- `.github/workflows/stale.yml:33`
- `.github/workflows/example-workflow.yml:11`
- `.github/workflows/example-workflow-envs.yml:11`

### script-injection (severity: high)

Multiple `run:` blocks interpolate GitHub Actions expressions directly into shell commands, enabling script injection.

**Sub-rule (a) — direct expression interpolation:**

`.github/workflows/automerge.yml`: Five `run:` steps pass `${{ github.event.pull_request.html_url }}` directly into `gh pr review` and `gh pr merge` shell commands. A malicious PR URL (e.g. containing shell metacharacters) would be executed by the shell before quoting can protect it.
- Line 25: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`
- Line 30: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
- Line 38: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`
- Line 43: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
- Line 52: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`

`.github/workflows/tag.yml`: The "Publish Git Tag" step interpolates a step output directly into the shell:
- `new_tag=${{ steps.bump-semver.outputs.new_version }}` — step outputs are workflow-controllable and must not be interpolated directly into `run:` scripts.

Locations:

- `.github/workflows/automerge.yml:25`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:43`
- `.github/workflows/automerge.yml:52`
- `.github/workflows/tag.yml:49`

### github-env-injection (severity: high)

In `.github/workflows/tag.yml`, the "Get Latest Tag" step writes the value of `$latest_tag` (derived from `git tag | sort -V | tail -n 1`) to `$GITHUB_ENV` without sanitization. Git tag names are attacker-controlled — a malicious tag name containing newlines could inject arbitrary environment variables into subsequent steps. The required sanitization step (`printf '%s' "$latest_tag" | tr -d '\n\r'`) is absent before the write.

Offending line: `echo "latest_tag=$latest_tag" >> $GITHUB_ENV`

Locations:

- `.github/workflows/tag.yml:38`

### permissions (severity: medium)

Two workflow files under `.github/workflows/` have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. This means the workflows run with the default (potentially broad) token permissions.

- `.github/workflows/example-workflow.yml`: no `permissions:` block at top level or job level
- `.github/workflows/example-workflow-envs.yml`: no `permissions:` block at top level or job level

Locations:

- `.github/workflows/example-workflow.yml:1`
- `.github/workflows/example-workflow-envs.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, permissions

**Notes:**

Fixed all 4 findings across 7 files:

1. **unpinned-uses**: Pinned all mutable references to full SHAs/digests:
   - action.yml: `docker://ghcr.io/cbrgm/mastodon-github-action:v2` → pinned with sha256 digest
   - go-binaries.yml: `cbrgm/pgp-sign-artifact-action@v1` → `@8aa5b853da0cd8e9754216b4a2f8c9ad1937439c` and `cbrgm/semver-tag-sync-action@v1` → `@a8dea5540c3183ed89913d8fb8d40ac05cf8126c`
   - tag.yml: `cbrgm/semver-bump-action@main` → `@cc89dae95968de9a49b9a4879290be60e1dd5600`
   - stale.yml: `cbrgm/cleanup-stale-branches-action@main` → `@1cd2068354f38284bb05b8ba279ae30790d68c44`
   - example-workflow.yml and example-workflow-envs.yml: `cbrgm/mastodon-github-action@main` → `@3ecf31935bd0ef42511601e24d8b676da3c1ce13`

2. **script-injection**: Moved all `${{ github.event.pull_request.html_url }}` and `${{ steps.bump-semver.outputs.new_version }}` expressions out of run: blocks into env: blocks, referencing them as plain shell variables.

3. **github-env-injection**: Added sanitization in tag.yml's 'Get Latest Tag' step using `printf '%s' "$latest_tag" | tr -d '\n\r'` before writing to $GITHUB_ENV.

4. **permissions**: Added `permissions: {}` to example-workflow.yml and example-workflow-envs.yml (these workflows only call the mastodon action with secrets, requiring no GitHub token permissions).

