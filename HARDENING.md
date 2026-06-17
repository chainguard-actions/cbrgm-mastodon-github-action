<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.1.26

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--mastodon-github-action/v2.1.26** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag (':v2') instead of an immutable SHA digest. This means the image pulled at runtime could change without notice, enabling supply-chain attacks. The reference 'docker://ghcr.io/cbrgm/mastodon-github-action:v2' should be replaced with a pinned digest such as 'docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced mutable Docker image tag 'docker://ghcr.io/cbrgm/mastodon-github-action:v2' with pinned digest 'docker://ghcr.io/cbrgm/mastodon-github-action@sha256:b84195e69ab9739b5f4d61013e804a060400a23654768c5c5d75244b5154692c' # v2 in action.yml line 43. The comment preserves the original tag for readability while the digest ensures immutability.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `cbrgm/mastodon-github-action@v2` to `cbrgm/mastodon-github-action@244bbe72e61b4490e2dc1c34f9537ae9299ae601 # v2` in both example workflow files: `example-workflows/example-inline-message.yaml` (line 11) and `example-workflows/example-multiline-message.yaml` (line 11). The SHA was resolved via git ls-remote.

