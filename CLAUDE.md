# Traccar Web (Vendored Fork — v6.12.2)

This is a **vendored fork** of [traccar-web](https://github.com/traccar/traccar-web), the official React 19 + MUI 7 + Redux Toolkit fleet admin UI that ships with Traccar. It is NOT the LOGISTICS.SY frontend — that is an Angular 18 app planned at `../docs/blueprint/v2/projects/frontend/CLAUDE.md`.

## Role in the LOGISTICS.SY platform

- **Dev/admin fleet view** — for our internal team to see vehicles, positions, geofences, and reports directly from Traccar without going through LOGISTICS.SY
- **Reference implementation** — how to use the Traccar WebSocket stream, draw geofences on MapLibre, and render device lists. Copy patterns (not code — Angular rewrite) into our Angular tracking components
- **Potentially embedded** — we may iframe specific pages (trip replay, geofence editor) into the LOGISTICS.SY admin portal for Phase 2 rather than rebuild them

**It is not exposed to shippers, carriers, or drivers.** Those users interact with the LOGISTICS.SY Angular app.

## Stack

- React 19 + Vite 8 + Redux Toolkit 2
- MUI 7 + `stylis-plugin-rtl` for RTL (Arabic/Hebrew) support
- MapLibre GL (upstream OSS fork of Mapbox GL JS) with `mapbox-gl-draw` for editing geofences
- `dayjs` for i18n dates, `exceljs` for report exports
- PWA via `vite-plugin-pwa`

## Build & run

```bash
npm install
npm start                 # vite dev server on :3000, proxies /api and /api/socket to :8082
npm run build             # production build to build/
npm run lint              # ESLint check
npm run lint:fix          # autofix
```

Dev proxy is in `vite.config.js`:
```js
proxy: {
  '/api/socket': 'ws://localhost:8082',
  '/api':        'http://localhost:8082',
}
```

So you need Traccar backend running at `localhost:8082` (the default REST+WebSocket port). `.env` sits alongside if you need to override — check before changing proxy.

## Directory map (`src/`)

| Path | What's in it |
|---|---|
| `App.jsx`, `index.jsx` | Vite entry + React root with Redux provider, theme, router |
| `AppThemeProvider.jsx` | **RTL-aware theme** — swaps emotion cache between `muiltr` and `muirtl` based on language direction |
| `common/components/LocalizationProvider.jsx` | Loads all locale JSONs + dayjs locales at startup; exposes `useLocalization()` with `direction` flag |
| `SocketController.jsx` | **WebSocket manager** — connects to `/api/socket`, reconnects with backoff, dispatches device/position/event actions. Read this before writing Angular tracking service. |
| `store/` | Redux slices: `devices`, `events`, `geofences`, `groups`, `session`, `drivers`, etc. One slice per Traccar entity. |
| `main/` | Main map page (`MainPage.jsx`, `MainMap.jsx`, `MainToolbar.jsx`, `EventsDrawer.jsx`) — the default landing view |
| `map/` | MapLibre helpers — `MapCamera`, `MapGeofence`, `MapMarkers`, `MapPositions`, `MapRoutePath`, `MapRoutePoints`, `MapScale`, plus `core/`, `draw/`, `geocoder/`, `legend/`, `notification/`, `overlay/`, `switcher/` subfolders |
| `settings/` | All CRUD pages: `DevicesPage`, `GeofencesPage`, `UsersPage`, `DriversPage`, `NotificationsPage`, `MaintenancesPage`, `CommandsPage`, etc. Mirror of Traccar backend REST resources. |
| `reports/` | Reports UI — trip/route/summary/events — with `TripReportPage.jsx` the most complex example |
| `login/` | Login, register, reset password, OIDC callback |
| `other/` | Less-common screens (emulator, replay, preferences) |
| `resources/l10n/` | 50+ locale JSON files. **`ar.json` is our primary target for LOGISTICS.SY reference**. |
| `common/attributes/` | `usePositionAttributes`, `useDeviceAttributes`, etc. — give localized labels/formats for each attribute |
| `common/util/` | Format helpers, converters, permissions, persisted state, feature flags |
| `simple/` | Minimal embeddable variant (not used by us yet) |

## Key patterns to copy into LOGISTICS.SY Angular tracking

1. **WebSocket with auto-reconnect** — see `SocketController.jsx`. Use `RxJS` in Angular; map to a shared `Subject<DevicePositionUpdate>`.
2. **RTL cache switching** — `AppThemeProvider.jsx` shows how to switch MUI emotion cache between LTR and RTL. Angular equivalent: switch `dir="rtl"` on `<html>` and use `angular/cdk/bidi`.
3. **Position list virtualization** — `DeviceList.jsx` uses `react-window` for 1000+ devices. Angular CDK `ScrollingModule` with `cdk-virtual-scroll-viewport`.
4. **MapLibre integration** — `map/core/MapView.jsx` is the base component. Same library (`maplibre-gl`) works in Angular — there is no Angular wrapper, use directly in a dumb component.
5. **Redux slice per domain entity** — NgRx equivalent, one feature per Traccar resource.

## Gotchas

- **React 19 + MUI 7 + Vite 8** — bleeding-edge stack. If dependencies break, check `package.json` `overrides` (currently pins `immer: 11.1.3`).
- **No TypeScript types in source** — `typescript` is in devDeps for type-checking JS via JSDoc only. Don't assume strict typing.
- **`stylis-plugin-rtl`** — only rewrites CSS direction; it does NOT flip icons or layouts. Components must still use logical properties or manual direction checks.
- **`@mapbox/mapbox-gl-rtl-text`** — Arabic text shaping on the map. Required for Arabic place names to render correctly.
- **`mui-file-input`** — an extra lib because MUI 7 removed native file input styling.
- **`react-qr-code` + `@yudiel/react-qr-scanner`** — used for device pairing (QR on device, scan from phone). LOGISTICS.SY should reuse for OsmAnd pairing per blueprint §13.4.
- **Native mobile shim** — `NativeInterface.js` handles iOS/Android webview bridges. We ignore; LOGISTICS.SY PWA runs in the browser.
- **Caching** — `CachingController.js` prefetches devices and positions on login. Watch out for memory on cold logins with 1000+ devices.

## Test strategy

Upstream has minimal tests. Don't invest in tests here — it's a fork we want mergeable. Write tests in the LOGISTICS.SY Angular app instead.

## License

Apache 2.0.
