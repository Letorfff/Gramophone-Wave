# Wave architecture and implementation decisions

## Product boundary

- **Gramophone** remains responsible for local library browsing and playback.
- **Wave** analyzes local listening behavior and presents recommendations. It must not turn Gramophone into a streaming client.
- **SpotiFLAC** remains a separate app responsible for resolving tracks through its configured providers, applying its own quality settings, and downloading to its user-selected output folder.

## Data model

Use profile-scoped persistence. The initial profiles are `RAP`, `PHONK`, and `UNASSIGNED`. A track identity is scoped by profile and should use a stable local media identifier/URI when available, with normalized title + artist as a fallback. Unique-track aggregates hold play count, qualified listening time, last-played time, skip count, completion count, and user feedback. Playback events should be accumulated from one tracked playback session and finalized on state transitions; pause, buffering, seek, and duplicate Media3 callbacks must not create additional plays.

Folder-to-profile associations are user-selected Android Storage Access Framework tree URIs with persisted grants. One profile may own multiple folders/playlists. Ambiguous or unassigned sources remain `UNASSIGNED`; moving a track changes profile association without deleting its accumulated data.

## Recommendation pipeline

1. Build compact per-profile preference summaries from local aggregates, positive/negative feedback, completion/listening ratio, recency, and skips.
2. Deduplicate and validate AI-returned suggestions before saving them under their originating profile.
3. Keep provider access behind a common interface. Store provider secrets with an Android Keystore-backed encrypted store; never commit keys or put them in build config.
4. Distinguish metadata search and outbound listening links from obtaining audio. Wave must not scrape protected streams, bypass DRM, or promise that a catalog API can download a track.

## SpotiFLAC hand-off

For the first integration, export selected recommendations as a playlist metadata file and let the user open SpotiFLAC and import it there. SpotiFLAC documents CSV and M3U/M3U8 import on its Home search and independently configures provider priority, quality, and output folder. Do not call SpotiFLAC's private Flutter `MethodChannel` from Gramophone or invent an external download Intent/API. See [`spotiflac-integration.md`](spotiflac-integration.md).

## App identity and distribution

The eventual Wave package must use an application ID distinct from upstream Gramophone. Preserve upstream license notices and comply with GPL-3.0 when distributing a modified Gramophone build. A repeatable Actions build may produce a debug APK for testing. Updates to a signed release require the same private signing key; store it only in GitHub Actions secrets.

## Build and verification status

This repository currently has no Gradle project or imported upstream Android source tree. No APK has been built and no runtime tests have been executed. These are explicit blockers, not passed checks. The upstream Gramophone source currently identifies itself as an offline local player and states it does not support download or streaming; integration must preserve that boundary.
