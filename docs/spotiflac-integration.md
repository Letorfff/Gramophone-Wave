# SpotiFLAC integration: verified first path

## Supported user flow

1. Wave creates a recommendation list and the user selects tracks.
2. Wave exports the selection as CSV or M3U8 metadata.
3. The user opens SpotiFLAC and imports the file using the import action from its Home search field.
4. SpotiFLAC resolves metadata using its installed providers. The user chooses provider/quality according to available settings and starts the download queue.
5. SpotiFLAC writes to its configured destination (by default its app folder under `Music/SpotiFLAC`, or a folder chosen with Android's folder picker). Gramophone can then rescan/refresh its local library.

## Why not directly enqueue from Gramophone?

SpotiFLAC's repository documents an app-internal Flutter/Android method channel, including `downloadByStrategy` and native queue methods. That is an implementation contract between SpotiFLAC's own Dart code and its Android host. The repository documentation does not define a supported third-party Intent/API for passing an arbitrary list, choosing a download provider/quality, and starting its queue. Calling the internal channel from another app would be unsupported and brittle. Do not claim direct queue integration unless SpotiFLAC maintainers publish such a contract.

## Export format notes

SpotiFLAC documents CSV and M3U/M3U8 import. Its M3U parser can build track suggestions from `#EXTINF` metadata, while CSV import enriches the imported rows with metadata providers. Export must contain metadata/search hints, not audio bytes or a source download URL. A robust CSV implementation should quote commas, quotes, and newlines according to CSV rules and include clear title/artist columns. M3U8 entries should use `#EXTINF` with `Artist - Title` text and avoid pretending that a recommendation is a local file path.

The integration should use Android's Storage Access Framework to let the user save/share the export and Android's normal app chooser to open/share it. First check whether SpotiFLAC is installed; if it is not, show its official project/release link rather than failing silently.

## Limits

- Available services, providers, and quality levels are configured in SpotiFLAC and may vary by track, extension, region, and account.
- Wave can search for metadata and offer platform links, but metadata availability does not imply a legal downloadable audio source.
- Do not use DRM circumvention, private endpoints, or undocumented download mechanisms.

## Upstream references

- [SpotiFLAC README: quality, playlist import, and local folders](https://github.com/spotiflac-app/spotiflac/blob/main/README.md#L231-L344)
- [SpotiFLAC Android channel contract](https://github.com/spotiflac-app/spotiflac/blob/main/android/README.md#L10-L40)
- [CSV/M3U import service](https://github.com/spotiflac-app/spotiflac/blob/main/lib/services/csv_import_service.dart)
- [Gramophone overview and local-only scope](https://github.com/FoedusProgramme/Gramophone/blob/beta/readme.md)
