# .github

CI and Renovate presets shared by the lares repos: the sidecars, hermes-agent-patched and lares itself.

## What is here

| Path | Used by |
| --- | --- |
| `.github/workflows/sidecar-renovate.yaml` | every sidecar's Renovate run |
| `.github/workflows/sidecar-python-ci.yaml` | Python sidecar CI (ruff, mypy, pytest) |
| `.github/workflows/sidecar-python-release.yaml` | Python sidecar image build on a tag |
| `.github/workflows/sidecar-python-auto-release.yaml` | monthly Python sidecar release |
| `.renovate/sidecarBase.json5` | every sidecar |
| `.renovate/sidecarPython.json5` | Python sidecars |
| `.renovate/sidecarCoreDep.json5` | repos that depend on nats-bridge-core |
| `.renovate/autoMerge.json5` | lares and, through `sidecarBase.json5`, every sidecar |

## Using it

Pin a tag, never `main`:

```yaml
uses: alexander-zimmermann/.github/.github/workflows/sidecar-renovate.yaml@<sha> # v1.0.0
```

```json5
"extends": ["github>alexander-zimmermann/.github//.renovate/sidecarBase.json5#v1.0.0"]
```

Renovate moves both to the next tag in one PR (group `shared ci`), and `autoMerge.json5` lets GitHub merge it once the repo's required check is green. A preset without `#tag` resolves against `main` on every run and is never bumped. Inside this repo, presets extend each other relatively (`./autoMerge.json5`), so they resolve at the same tag as the preset that extends them.

## Releases

`auto-tag.yaml` tags every merge to `main` that changes a shared file: a patch, a minor if a commit since the last tag is a `feat`, a major for `!` or `BREAKING CHANGE`. Renovate only auto-merges minor and patch, so a major waits for a hand in every consumer. A merge that changes only this repo's own files (README, its own CI) gets no tag.
