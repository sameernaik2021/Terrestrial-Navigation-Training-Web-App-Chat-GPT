# TerraNav Learning Lab — Terrestrial Navigation Training Web App

A browser-based student training suite implementing the six ideas listed in **Software ideas for Terrestrial Navigation.txt**. Built with HTML, CSS and vanilla JavaScript; no framework, build step, API key or backend is required.

## Included simulators

1. **Latitude & Longitude Trainer** — Decimal-degree validation, DMS conversion, synthetic plotting grid and coordinate-order quiz.
2. **Compass Bearing Calculator** — Whole-circle bearings, 16-point compass labels, compass rose, declination and deviation corrections using an explicitly documented signed convention.
3. **Land Navigation Simulator** — Bearing/distance movement, current position, trail, checkpoints and reset on a synthetic east/north grid.
4. **Distance, Time & Speed Calculator** — D = S × T, unit normalisation and distance conversion for kilometres, metres, nautical miles and statute miles; speed units include km/h, m/s, knots and mph.
5. **Position-Fixing & Triangulation Tool** — 2D resection from bearings to known landmarks, least-squares line intersection and synthetic observation-error experiments.
6. **GPS Navigation Practice App** — Synthetic position, waypoint distance/bearing, simulated movement and optional one-time browser geolocation permission.

Each module opens in a **new tab** from the dashboard. Training progress is saved locally using `localStorage` where available.

## Run it

### Option A — open locally
Extract the ZIP and open `index.html` in a modern browser. Most functions work offline. Browser geolocation is generally restricted to secure contexts (HTTPS or localhost), so the GPS module may require Option B.

### Option B — serve locally (recommended for geolocation)
From this directory, run:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000` in your browser. Stop the server with `Ctrl+C`.

## Data, accuracy and safety

- The training grid, checkpoints, landmarks and waypoint scenarios are **synthetic**. They are not real mapped features or verified routes.
- The coordinate trainer's plotting axes are relative training units; they do not project the Earth or place the entered coordinates on a real map.
- Land movement uses a local flat-grid approximation, with north up and east right. Terrain, road networks, elevation and obstacles are not modelled.
- Bearing conventions are shown in the interface. In the bearing calculator, east declination is positive and `magnetic = true − declination`; compass deviation is applied as `compass = magnetic − deviation` where east deviation is positive. For actual work, follow the conventions supplied with the instrument/map.
- The triangulation tool uses a simplified least-squares intersection of lines. It is a teaching aid, not surveying software.
- The GPS tool uses the browser Geolocation API only after the user explicitly selects device mode and presses **Update position**. The app does not transmit the reading. Browser geolocation can be estimated from GPS, Wi-Fi, cellular or other signals and is not necessarily GPS-only.
- **Do not use this app as the sole aid for real-world navigation, emergency response, surveying or safety-critical decisions.**

## Design principles applied

- **Separation of concerns / SRP:** each simulator has a focused renderer and domain calculations are small functions.
- **KISS / YAGNI:** no backend, account system, framework or unnecessary infrastructure.
- **DRY:** shared formatting, validation helpers, layout and SVG grid helpers.
- **Modularity and clear boundaries:** separate `index.html`, `app.html`, `styles.css` and `app.js`; each simulator is independently selected with a query parameter.
- **Secure by design:** no third-party API, no secrets, escaped dynamic text for user-facing labels, and opt-in geolocation.
- **Testability:** deterministic formula functions and manual regression checks documented below.
- **Observability:** clear inline validation/error messages and transparent data-source labels.
- **Evolutionary architecture:** individual renderers can be extended without rewriting the dashboard.

## Files

- `index.html` — dashboard and new-tab module links.
- `app.html` — shared shell for each module.
- `styles.css` — responsive visual system.
- `app.js` — simulator logic.
- `README.md` — setup, scope, assumptions and test checklist.

## Validation checklist

Run these checks after opening the dashboard:

1. Each of the six cards opens the correct module in a separate tab.
2. Coordinate conversion: `19.5` latitude displays `19° 30′ 00.00″ N`; values outside ±90 latitude or ±180 longitude are rejected.
3. Bearing conversion: true `080°`, declination `5° E`, deviation `0°` gives magnetic `075°`; north wraps correctly around 000°/360°.
4. Land movement: from E `0`, N `0`, a `090°` bearing for `2` units moves east by `2` units; `000°` moves north.
5. Speed: `180 km / 60 km/h = 3 hours`; `20 knots = 37.04 km/h`.
6. Triangulation: zero-error bearings converge near the hidden synthetic reference; increasing error changes the estimate.
7. GPS: synthetic mode works without permission; device mode only requests location after user action; denied/unavailable location shows a useful message.
8. Local storage: progress counters persist after refresh where storage is enabled.
9. Responsive layout: cards and simulator panels reflow on narrow screens.

## Limitations

This is a student simulator, not a production navigation product. No live basemap, routing engine, map datum transformation, elevation model, magnetic-field model, GPS receiver integration or offline tile package is included.