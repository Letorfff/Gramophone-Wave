# Gramophone Wave

Gramophone Wave is intended to preserve Gramophone as a local-first Android player and add a separate Wave discovery layer. Wave recommendations are metadata and search suggestions; downloads must be carried out by a user-selected, legally available source.

## Project status

This repository is a newly created integration workspace. It does **not yet contain the Gramophone Android source tree or a buildable APK**. No compile, device install, playback, AI-provider, or download integration checks have been run. See [`docs/architecture.md`](docs/architecture.md) for the implementation decisions and [`docs/spotiflac-integration.md`](docs/spotiflac-integration.md) for the verified first integration path.

## Intended first milestone

1. Import the upstream Gramophone source with its GPL-3.0 license and keep its existing player intact.
2. Add Wave as an in-app feature with local profile-separated listening data.
3. Export selected recommendation metadata as CSV/M3U8 and hand off to SpotiFLAC's documented import flow.
4. Add provider-backed AI only after the local profile and recommendation pipeline is in place.

## Upstream projects

- [Gramophone](https://github.com/FoedusProgramme/Gramophone) — upstream branch `beta`.
- [SpotiFLAC](https://github.com/spotiflac-app/spotiflac) — upstream branch `main`.

## Verification policy

Build and device checks will be reported only after they actually run. Release signing credentials must stay in private GitHub Actions secrets and must never be committed.
