# Lodestone Compass

A single-file, browser-based digital compass that reads live device orientation data and renders a 16-point compass rose with smooth animated rotation.

---

## Features

- **16-point compass rose** — cardinal (N/E/S/W), intercardinal (NE/SE/SW/NW), and 30° degree markers, drawn as SVG.
- **True-north detection** — distinguishes `webkitCompassHeading` (iOS, true north) and `absolute` (`deviceorientationabsolute`, magnetometer-backed) from relative-only fallback, and surfaces which one is active.
- **Smooth interpolated rotation** — single `requestAnimationFrame` loop with shortest-path angle lerp (handles 359°→0° wraparound without snapping).
- **Full error/permission handling**:
  - HTTPS / secure-context check
  - `DeviceOrientationEvent` support check
  - iOS 13+ permission prompt flow (`requestPermission`)
  - 4s no-data timeout → explicit sensor-missing error
- **Fully responsive** — dial scales against `vw` and `vh`, `clamp()`-based type sizing, safe-area insets, landscape/short-viewport and tablet/desktop media queries.
- **No dependencies** beyond Google Fonts (Space Mono + Inter) — zero build step.

---

## Files

| File          | Purpose                                  |
|---------------|-------------------------------------------|
| `compass.html`| Complete app — HTML, CSS, JS in one file |

---

## Usage

1. Serve `compass.html` over **HTTPS** (or open via `localhost`) — device orientation APIs are blocked on insecure origins.
2. Open on a mobile browser (desktop browsers generally don't emit orientation events).
3. Tap **Enable Compass**.
   - iOS: grants a permission prompt — accept it.
   - Android: listens on `deviceorientationabsolute` if available, else falls back to `deviceorientation`.
4. Heading (°) and 16-point label render live in the HUD; the dial rotates so **N** always faces true/relative north depending on sensor support.

No install, no build tooling — just host the static file.

---

## Known Limitations

- **Relative-only fallback**: devices without a magnetometer (or browsers withholding `absolute` data) report heading relative to the orientation at activation, not true north. The UI flags this explicitly (`"Uncalibrated heading"` in amber) — do not treat it as a reliable bearing.
- **No offline/PWA support** — Google Fonts CDN requires network on first load; no service worker/caching layer.
- **Not tested on desktop** — no fallback UX for devices with zero orientation sensors beyond the error message; there is no manual/mouse-drag mode.
- **iOS permission is session-scoped** — refreshing the page re-triggers the permission prompt (standard iOS Safari behavior, not a bug in this app).

---

## Browser Support

| Platform          | Heading Source                  | True North |
|-------------------|----------------------------------|------------|
| iOS Safari 13+    | `webkitCompassHeading`           | Yes        |
| Android Chrome     | `deviceorientationabsolute`      | Yes (if magnetometer present) |
| Android (no magnetometer) | `deviceorientation` (relative) | No — flagged in UI |
| Desktop browsers  | Unsupported                      | N/A — shows error |

---

## Tech Stack

- Vanilla HTML / CSS / JavaScript (no framework, no build step)
- SVG for the compass rose (generated programmatically at load)
- Google Fonts: Space Mono (numerals/technical labels), Inter (UI text)
