<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.25

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--mastodon-github-action/v2.1.25** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml Docker action references a mutable image tag (`v2`) instead of a SHA digest. The image `docker://ghcr.io/cbrgm/mastodon-github-action:v2` can be silently replaced with a different (potentially malicious) image at any time, making this a supply-chain risk. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>`

Locations:

- `action.yml:40`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag 'docker://ghcr.io/cbrgm/mastodon-github-action:v2' with the immutable SHA256 digest 'docker://ghcr.io/cbrgm/mastodon-github-action@sha256:b84195e69ab9739b5f4d61013e804a060400a23654768c5c5d75244b5154692c' in action.yml line 40. The original tag is preserved as a comment (# v2) outside the YAML quotes for readability.

