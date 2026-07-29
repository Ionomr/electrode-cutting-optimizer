# Electrode Sheet Optimizer

A browser-based tool for calculating how many electrodes can be cut from a sheet of membrane material using Ionomr's rule dies. Supports mixed die types, minimum electrode constraints, and multi-sheet comparison — no installation required.

---

## Getting started

Open `electrode_optimizer.html` in any modern browser (Chrome, Firefox, Safari, Edge). No internet connection is required for core functionality. The ZIP export requires an internet connection to load the JSZip library.

---

## Die types

The tool has four dies configured with their electrode dimensions and grid geometry:

| Die | Grid | Electrodes/stamp | Electrode size (mm) | Gap X / Y (mm) | Full grid footprint (mm) |
|-----|------|-----------------|--------------------|-----------------|-----------------------|
| TP5 | 4×5 | 20 | 22.1 × 22.1 | 5.0 / 5.0 | 103.4 × 130.5 |
| TP50v1 | 1×2 | 2 | 70.47 × 70.47 | 0 / 10.0 | 70.47 × 150.94 |
| TP50v2 | 2×2 | 4 | 54.89 × 98.17 | 5.0 / 5.0 | 114.78 × 201.34 |
| CT25 | 3×2 | 6 | 49.75 × 49.75 | 10.0 / 10.0 | 169.25 × 109.5 |

All dimensions are derived from the electrode grid geometry only — the outer die backer board dimensions are not used. Pitch = electrode size + gap.

Physical constraints applied automatically:
- **4 mm edge margin** on all sides of the sheet
- **8 mm gap** between die zones
- **No partial electrodes** — every electrode cut is complete
- **Partial die grids allowed** — the die can be aligned so only part of the grid lands on material (e.g. 5 electrodes from a 4×5 die)
- **Die rotation** — each stamp can be rotated 90° if it fits better

---

## Sheet dimensions

Enter width and height in **cm**. Use the preset buttons for standard paper sizes:

- **A3** — 42.0 × 29.7 cm
- **A4** — 29.7 × 21.0 cm
- **A5** — 21.0 × 14.85 cm

The usable area (after margin) is shown as you type.

---

## Sheet name and Lot #

Each sheet tab has a name field and a Lot # field. The sheet name appears in the tab, in exported PNG filenames, and in the CSV export. The Lot # is included in the CSV. Double-click a tab to rename it.

---

## Calculating a layout

### Calculate layout

Places the exact number of electrodes you specify — no more. For each enabled die type, set a minimum electrode count. The optimizer:

1. Places the mandatory electrodes first, using the fewest and most physically natural die stamps (largest sub-grid that satisfies the count)
2. If a minimum can't be satisfied in a single region, it splits the placement across multiple regions and carries the remainder forward
3. Stops once all minimums are met — remaining space is left empty

If no minimums are set, it runs a full greedy fill and shows the maximum possible yield.

### Fill remaining space

Takes the current layout exactly as-is and fills any leftover regions with the best-fitting stamps from all enabled dies. Existing zones are never moved or changed. Use this to add bonus electrodes after specifying your required counts.

**Tip:** For the best combined layout when using multiple die types, set minimums for all die types together and use **Calculate layout** — this lets the optimizer find an arrangement that accommodates all dies simultaneously, which produces better results than filling sequentially.

### Clear

Resets all minimum electrode inputs to zero and clears the layout diagram.

---

## Layout diagram

Each coloured rectangle in the diagram is one die stamp — one physical press of the die. The electrode cutouts are shown inside each zone. A dashed border shows the 4 mm sheet margin.

- **Zone label** — shows the die name and electrode count for that stamp
- **↺** — indicates the stamp is rotated 90°
- **Partial tag** (in the zone breakdown table) — the die was aligned so only part of its grid lands on material

The diagram can be zoomed using the slider if the sheet is wide relative to your screen.

---

## Multiple sheets (tabs)

Click **+** to add a sheet tab. Each tab is fully independent with its own dimensions, die settings, and layout. Use tabs to compare different sheet sizes or die configurations side by side.

- Click a tab to switch to it
- Double-click a tab name to rename it
- Click **✕** on a tab to remove it
- Tabs scroll horizontally with **‹ ›** arrows when there are too many to display

---

## Exporting results

### Export PNG (individual)
Saves the layout diagram for the current sheet as a PNG image at 3 px/mm resolution. The filename includes the sheet name and dimensions.

### All layouts as ZIP
Packages the layout diagrams for all calculated sheets into a single ZIP file.

### Export all as CSV
Exports a single CSV file with one row per sheet. Columns: **Sheet, Lot #, Width (cm), Height (cm), Total Electrodes, TP5, TP50v1, TP50v2, CT25, Area Used (%)**.

> **Note:** Exports use data URLs and should work when the file is opened locally (`file://`). The ZIP export uses a CDN library and requires an internet connection.

---

## Hosting and sharing

The tool is a single self-contained HTML file. To share it:

- **Send the file directly** — recipients open it in any browser, nothing to install
- **Google Drive** — upload and share a link; recipients download and open locally
- **GitHub Pages** — upload as `index.html` to a public repository, enable Pages in Settings → Pages → Deploy from branch. The tool is then available at `https://yourusername.github.io/repo-name`
- **Netlify Drop** — drag the file to [netlify.com/drop](https://netlify.com/drop) for an instant shareable URL

Custom domains are supported on both GitHub Pages and Netlify.

---

## Limitations

- The optimizer uses a guillotine packing algorithm. This is fast and produces good results but is not guaranteed to find the global optimum for complex multi-die layouts.
- Sequential fill (using "Fill remaining space" after "Calculate layout") may leave more waste than a joint optimization. For best results with multiple die types, set all minimums together and use **Calculate layout**.
- Very large electrode counts with many mandatory dies may be slow due to permutation testing across packing directions.
