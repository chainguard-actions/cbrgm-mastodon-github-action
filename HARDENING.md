<!-- markdownlint-disable -->

# Hardening Report: cbrgm--mastodon-github-action/v2.2.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--mastodon-github-action/v2.2.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The `runs.image:` field in action.yml references the Docker image `docker://ghcr.io/cbrgm/mastodon-github-action:v2` using a mutable version tag (`:v2`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image if the tag is moved. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/cbrgm/mastodon-github-action@sha256:<64-hex-char-digest> # v2`.

Locations:

- `action.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from 'docker://ghcr.io/cbrgm/mastodon-github-action:v2' to 'docker://ghcr.io/cbrgm/mastodon-github-action:v2@sha256:5aa317495e11e249852bdc76b9a529df74fc3d0e58ad6984b63fb719af4417bd'. The docker:// scheme and :v2 tag are preserved inline, with the immutable SHA digest appended.

