# 🌍 Coordinate Converter

A clean, fast, single-file web application to convert between **Google Maps (Latitude / Longitude)** and **MGRS (Military Grid Reference System)** coordinates — right in your browser.

---

## ✨ Features

### 🔄 Bidirectional Conversion
- **Lat/Lng → MGRS** with selectable precision (1 m to 10 km)
- **MGRS → Lat/Lng** returning the **center of the MGRS cell** for stable, symmetric round-trips
- Paste Google Maps-style coordinates (`40.7128, -74.0060`) directly — automatically parsed into lat/lng fields

### ✅ Strict Validation & Round-Trip Verification
- Lat bounded to `-80..84` (MGRS valid zone); Lng bounded to `-180..180`
- Full MGRS format validation — zone (1–60), latitude band (C–X, no I/O), 100 km square letters (no I/O), even-length numeric pairs
- After every conversion, the result is converted back and **drift is measured and displayed**
- ✓ Green badge for accurate round-trips · ⚠️ Amber warning when drift exceeds the precision cell

### 🗺️ Open in Google Maps
- Every result shows a 🗺️ button that opens the coordinate in Google Maps in a new tab
- History entries include the same map link

### 📋 Copy to Clipboard
- One-click copy on every result (MGRS, Lat/Lng, Google Maps URL)
- History entries expose separate **Copy From** and **Copy To** buttons for granular access
- Graceful fallback via `document.execCommand` for older environments

### 📜 Conversion History
- Last **30 entries** persisted in `localStorage`
- Each entry shows:
  - Conversion direction badge (LL → MGRS or MGRS → LL)
  - **Relative time** (e.g., "3 minutes ago") that auto-refreshes every 30 seconds
  - **Full localized date/time** (e.g., "Oct 24, 2025 3:45 PM")
  - Inline actions: Copy From · Copy To · Open in Maps · Delete
- Click an entry's body to reload it into the input fields
- "Clear All" button with confirmation

### 🎛️ Layout & State
- Toggle between **horizontal** (3-column) and **vertical** (stacked) layouts
- Responsive design — horizontal layout collapses to single column on narrow screens
- All inputs, outputs, history, and layout preference are persisted between sessions

### 🎨 Design
- Modern glassmorphism UI with animated gradient background
- Monospace results for easy reading of coordinate strings
- Subtle toasts for clipboard confirmations and errors
- SVG favicon embedded inline — no external asset fetches

---

## 🚀 Quick Start

### Option 1 — Open directly
Clone or download the repo, then double-click the HTML file. That's it.

```bash
git clone https://github.com/mgrsjs/mgrsjs.github.io.git
cd mgrsjs.github.io
# Open index.html in your browser
```

### Option 2 — GitHub Pages
The repository is live at **[mgrsjs.github.io](https://mgrsjs.github.io)**.

---

## 📖 Usage

### Convert Lat/Lng to MGRS
1. Enter latitude and longitude — *or* paste Google Maps coordinates like `40.7128, -74.0060`
2. Pick the desired **MGRS precision**:
   - `1 m (5 digits)` — e.g., `18TWL8085712345`
   - `10 m (4 digits)` — e.g., `18TWL80851234`
   - `100 m (3 digits)` — e.g., `18TWL808123`
   - `1 km (2 digits)` — e.g., `18TWL8012`
   - `10 km (1 digit)` — e.g., `18TWL81`
3. Click **Convert to MGRS**
4. Review the result, round-trip verification, and copy or open in Google Maps

### Convert MGRS to Lat/Lng
1. Enter an MGRS coordinate (e.g., `18TWL8085712345`) — spaces and lower case are OK
2. Click **Convert to Lat/Lng**
3. The result is the **center of the MGRS cell** at the given precision, along with a ready-to-share Google Maps link

### Keyboard shortcut
Press **Enter** inside any input to trigger the matching conversion.

---

## 🧮 How It Works

The MGRS engine is implemented from scratch against the NGA / USGS spec, built on top of **proj4js** for robust WGS84 ↔ UTM transformations.

### Coordinate pipeline
```
Lat/Lng (WGS84)  ──▶  UTM zone + easting/northing  ──▶  MGRS grid square + numeric
                    proj4js                               (100 km letter scheme)
```

### 100 km square letter scheme
The MGRS spec uses three alternating column-letter sets and two alternating row-letter sets across UTM zones:

| Zone set | Zones        | Column letters |
|----------|--------------|----------------|
| Set 1    | 1, 4, 7, …  | `ABCDEFGH`     |
| Set 2    | 2, 5, 8, …  | `JKLMNPQR`     |
| Set 3    | 3, 6, 9, …  | `STUVWXYZ`     |

| Zone parity | Row letters              |
|-------------|---------------------------|
| Odd zones   | `ABCDEFGHJKLMNPQRSTUV`   |
| Even zones  | `FGHJKLMNPQRSTUVABCDE`   |

Handled correctly for all 60 zones plus **Norway (zone 32)** and **Svalbard (zones 31/33/35/37)** UTM exceptions.

### Round-trip symmetry
When decoding MGRS back to Lat/Lng, the engine returns the **center of the cell** rather than a corner. This makes subsequent re-encoding at the same precision deterministically produce the **exact same MGRS string**, verifiable via the built-in round-trip badge.

---

## 💾 State Persistence

Everything is stored under a single `localStorage` key (`coordConverter_state_v4`):

- Input values (lat, lng, google maps string, MGRS string, precision)
- Last computed outputs
- Conversion history (up to 30 entries)
- Orientation preference (horizontal / vertical)

No cookies. No external storage. No network requests beyond the initial CDN library load.

---

## 🔒 Privacy

- **100% client-side** — coordinates never leave your browser
- The Google Maps "open in map" button uses a simple `https://www.google.com/maps?q=lat,lng` URL and only loads when you explicitly click it

---

## 🛠️ Project Structure

This project is intentionally a **single HTML file** containing all markup, styling, and logic. That makes it:

- Trivially portable (drop it on any static host, email it, stash it on a USB stick)
- Auditable at a glance
- Free of build steps, toolchains, or dependencies beyond two small CDN libraries

---

## 📄 License

MIT
