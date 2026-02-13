# SubMusic

Garmin Connect IQ music player app for syncing and playing music offline from personal music servers (Subsonic, Ampache, Plex).

## Language & Platform

- **Language:** Monkey C (Garmin's proprietary language)
- **Platform:** Garmin Connect IQ (smartwatch apps)
- **SDK:** Garmin Connect IQ SDK (https://developer.garmin.com/connect-iq/)

## Build

Requires the Garmin Connect IQ SDK. Build via Connect IQ IDE or CLI:

```bash
monkeyc -o bin/SubMusic.prg -w -z manifest.xml
```

Build config is in `monkey.jungle` (points to `manifest.xml`). No external package manager - all dependencies are part of the Connect IQ SDK (`Toybox.*`).

## Testing

- Use the Connect IQ Simulator for testing on virtual watch models
- Deploy to a real Garmin watch with Developer Mode enabled for device testing
- Enable `debug` setting in `resources/settings.xml` for debug logging via `System.println()`

## Project Structure

```
source/                  Main Monkey C source code
  Api/                   API backend implementations (Subsonic, Ampache, Plex)
  Store/                 Data persistence layer wrapping Application.Storage
  Sync/                  Sync logic (audio, playlists, podcasts, artwork, scrobbles)
  View/                  UI/Menu views (menus, browse, settings, playback)
  SubMusicApp.mc         App entry point (extends AudioContentProviderApp)
  SubMusicProvider.mc    Provider factory for API backends
  Deferrable.mc          Async task pattern (deferred/promise-like)
  Song.mc / Episode.mc   Data models
resources/               UI resources, strings, settings, drawables
manifest.xml             App manifest (version, permissions, supported devices)
```

## Architecture

- **Multi-Provider Pattern:** Strategy pattern in `Api/` - base `Api.mc` class with `SubsonicProvider`, `AmpacheProvider`, `PlexProvider` implementations. `SubMusicProvider.mc` is the factory.
- **Deferred Async Pattern:** `Deferrable.mc` / `DeferredFor.mc` for async task chaining (defer/complete/cancel). Used by all Sync classes.
- **Storable Persistence:** `Storable.mc` base class for serialization; `Store/` classes wrap `Application.Storage` for typed data access.
- **Error Hierarchy:** `Error` -> `ApiError`, `HttpError`, `GarminSdkError` in `SubMusicError.mc`.

## Code Conventions

- **Indentation:** Tabs
- **Braces:** K&R style (opening brace on same line)
- **Classes/Files:** PascalCase, one class per file, file named after class
- **Methods:** camelCase (e.g., `getPlaylistSongs()`)
- **Member variables:** snake_case with prefix: `d_` (private), `f_` (function/callback), `s_` (static)
- **Constants:** UPPER_SNAKE_CASE in enums
- **Visibility:** `hidden` keyword for private members
- **Modules:** `module SubMusic { module Provider { ... } }`
- **Debug logging:** `if ($.debug) { System.println(...); }`
- **Imports:** `using Toybox.X` and `using SubMusic.Y` at top of file
