# WatchVerse Desktop — macOS

Native macOS desktop wrapper for [WatchVerse](https://watchverse.watch) with
Discord Rich Presence. Frameless Chromium window with native traffic-light
controls; pushes custom Discord states for watching a **film/série/animé**, the
**TV en direct**, and the **Watch Party**.

## Stack

- **Electron 41** + **discord-rpc**
- **Discord App ID** : `1510782927779139705`
- **Large image asset key** : `big_image` (WatchVerse logo)
- **Bundle ID** : `com.watchverse.desktop`

## Setup & run

```bash
cd "WatchVerse Webview MacOS"
npm install
npm start      # https://watchverse.watch
npm run dev    # localhost:5173 + DevTools
```

Env vars: `WATCHVERSE_URL`, `WATCHVERSE_DEV`, `WATCHVERSE_PUBLIC_URL`,
`WATCHVERSE_API`, `WATCHVERSE_DISCORD_INVITE`, `WATCHVERSE_DESKTOP_REPO_MAC`.

## Build

```bash
npm run build:mac          # arm64 + x64 DMGs
npm run build:mac:arm64    # Apple Silicon
npm run build:mac:x64      # Intel
npm run build:mac:dir      # unpacked .app (quick local test)
```

Ad-hoc signed (`mac.identity: "-"`, `CSC_IDENTITY_AUTO_DISCOVERY=false`) so
Apple Silicon Macs launch it. First launch needs Gatekeeper bypass (not
notarized):

```bash
xattr -cr /Applications/WatchVerse.app
```

> macOS DMGs are best built **on a Mac**. From Windows, `--dir` works but the
> `.dmg` packaging step needs macOS tooling.

## Discord Rich Presence

Identical detection to the Windows wrapper (see that README for the full
table). URL-based + page-emitted (`window.watchverse.setPresence`, with the
`window.scanverse` back-compat alias). Legacy route names `manga`/`reader` map
to `title`/`player`. Detect macOS via `window.watchverse?.platform === 'darwin'`.

Custom states: `Regarde <Titre>` with `S01E03 — titre` for series, `👥 Watch
Party · <Titre>` (with a "Rejoindre la Watch Party" button → `/party/CODE`),
and `📺 Regarde la TV en direct`.

## `watchverse://` deep links

Registered in Info.plist via `mac.protocols`:
`watchverse://film/<id>` (serie/anime/title), `watchverse://watch/<id>/s1e3`,
`watchverse://party/<CODE>`, `watchverse://tv`. Routed via `app.on('open-url')`.

## In-app updater

Polls the GitHub Releases feed of `WATCHVERSE_DESKTOP_REPO_MAC` (default
`ScanVerseFrance/WatchVerse-Desktop-MacOS`), downloads the arch-matched `.dmg`,
opens it in Finder and quits (drag-to-Applications flow — no silent upgrade for
unsigned macOS apps). The repo + its releases must be **public** since the
updater fetches the Releases API without a token.

## Project layout

```
src/
  main.js            Electron main (window, IPC, navigation, native menu)
  preload.js         contextBridge → window.watchverse + injected title bar
  routes.js          route → Discord Rich Presence payload mapping
  rpc.js             discord-rpc wrapper with auto-reconnect + heartbeat
  update-check.js    GitHub Releases poll + DMG download orchestration
  update-preload.js  contextBridge for the update modal window
  update-ui/         HTML/CSS/JS of the branded update modal
assets/
  icon.png           WatchVerse logo (electron-builder converts to .icns)
  license-fr.txt     End-user license
build/
  entitlements.mac.plist  Entitlements (for future signed builds)
```

## TODO before shipping

- Confirm the **Discord invite** (`WATCHVERSE_DISCORD_INVITE`).
- The **updater repo** `ScanVerseFrance/WatchVerse-Desktop-MacOS` must be public
  with `.dmg` release assets.

## License

UNLICENSED — © 2026 Team WatchVerse.
