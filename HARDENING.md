<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.27

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--mastodon-github-action/v2.1.27** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag (`docker://ghcr.io/cbrgm/mastodon-github-action:v2`) instead of an immutable SHA256 digest. This means the image pulled at runtime could change without notice, enabling supply-chain attacks. It should be pinned to a specific digest, e.g. `docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `ghcr.io/cbrgm/mastodon-github-action:v2` with the immutable SHA256 digest `ghcr.io/cbrgm/mastodon-github-action@sha256:b84195e69ab9739b5f4d61013e804a060400a23654768c5c5d75244b5154692c` in action.yml line 44. The original tag is preserved as a comment `# v2` outside the YAML quotes for readability.

