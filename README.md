# MC Pump Sizing Calculator

An interactive, browser-based calculator for sizing a **medium-consistency (MC) stock pump**, its discharge piping, the drive motor and the VFD. Built on the AHLSTROM / Kamyr procedure with Bodenheimer's friction-loss formula.

Everything runs in the browser — no server, no data upload. Enter a duty on the left, results update live on the right. You can also export the calculation as an Excel workbook.

## Live demo

Once you push this repo to GitHub and enable Pages, the calculator will be live at:

`https://<your-github-user>.github.io/<repo-name>/`

## Features

- **US / SI unit toggle** in the header. US units by default (°F, ft, inches, GPM, HP, lb/ft³, ADST/d); one click switches to SI (°C, m, mm, m³/h, kW, kg/m³, ADt/d). Every input, unit label and result updates in place.
- **Bleached Pulp** included in the stock-type list with F1 = 0.9.
- Live calculation as you type — total dynamic head, per-section pipe friction (Bodenheimer), pump hydraulic and shaft power, motor sizing (auto-select next IEC standard frame), VFD rating and full-load current, and affinity-law speed-turndown analysis.
- Add / remove pipe sections dynamically. Each section carries its own diameter, length, fittings, in-line equipment and flow fraction; the 0.5 ft/s anti-pulsation velocity rule can be applied or excluded per section (turn it off for a suction / tower-bottom line).
- Automatic warnings for sections that fall below 0.5 ft/s (pulsation risk) or run above the 1.65 ft/s plug-flow band.
- **Download Excel (`.xlsx`)** — full workbook (Inputs / Results / Piping / Turndown), dual units, via SheetJS.
- **Download PDF** — one-click formatted engineering report via jsPDF + autoTable, in whichever unit system is currently selected.
- Download the current inputs as a `params.json` file for archiving or replay.

## Running locally

Any static-file server works. The simplest options:

```bash
# Option A: Python
python3 -m http.server 8000
# then open http://localhost:8000/

# Option B: Node
npx serve .
```

Or just open `index.html` directly in the browser — the SheetJS CDN needs internet access; everything else works offline.

## Deploying to GitHub Pages

1. Create a public GitHub repository (e.g. `mc-pump-sizing`).
2. Copy the contents of this folder into the repo root and push.
3. In the repo, go to **Settings → Pages** and set the source to `main` branch, `/ (root)`.
4. GitHub will publish the site at `https://<user>.github.io/<repo>/` within a minute or two.

The included `.nojekyll` file tells GitHub Pages to skip Jekyll processing and serve the files as-is.

## Method

The calculation implements the procedure documented in **AHLSTROM / Kamyr, *"MC Pumps & Pipelines"*, K. Serenius, 19 April 1990** — the inter-office memo covering pump calculations, line sizing, pipe classes and installation for medium-consistency stock systems. Only the calculation method is reproduced here; the source document is *not* included in this repository — obtain a copy through appropriate channels if you need the original text and correction curves.

Key formulas used:

- **Friction gradient (Bodenheimer):** `hf = 4.72 × F × Q^0.15 × C^2.5 / D^1.3`  (ft head per 100 ft of equivalent pipe; Q in US GPM, D in inches, C in % BD).
- **Stock factor:** `F = F1 × F2 × F3` where F1 = stock-quality factor (table), F2 = pH factor (cubic fit between pH 7 and pH 10), F3 = `1.6 − 0.006 × T(°F)`.
- **Stock flow:** `Q (GPM) = 16.51 × Production(air-dry t/d) / Consistency(%)`.
- **Fittings vs equipment:** bends, tees and Ys are handled as *equivalent length* (added to pipe length, then multiplied by hf). Control valves, mixers, meters and reducers are *actual head loss* in feet, added directly to the required head. Mixing the two up is the single most common error.
- **Anti-pulsation rule:** minimum MC pipeline velocity is 0.5 ft/s (0.15 m/s); below it the plug flow breaks into stick/slip pulsation that damages pipe supports. This sets the VFD's minimum safe speed.
- **Consistency > 12%:** vendor pump curves are drawn for 12% — apply the head (Ch) and power (Cp) correction factors from the vendor's curve before final selection.
- **Motor sizing:** required = brake power × drive-eff × 1.2 (memo recommends 20% margin); the calculator picks the next standard IEC frame (90, 110, 132, 160, 200, 250, 315, 355, 400, 450, 500, 560, 630, 710, 800 kW).

## Validity and cautions

- Bodenheimer's formula is a turbulent-regime correlation; treat results below roughly 1 ft/s with caution.
- Temperature is critical — friction climbs ~10% per 10 °C of cooling. Always size on a realistic *low* operating temperature, never the ideal design value.
- NPSH for MC pumps is governed by the integral degassing system, not conventional NPSHa. Confirm with the pump vendor.
- This calculator sizes the *duty*; always verify the final pump, brake power and NPSHr against the manufacturer's pump curves and check the maximum power along the curve.

## File list

```
mc-pump-sizing/
├── index.html   — the entire calculator (HTML, CSS, JS inline)
├── README.md    — this file
└── .nojekyll    — tells GitHub Pages not to run Jekyll
```

External dependencies (loaded from CDN): [SheetJS](https://sheetjs.com/) for the `.xlsx` export, [jsPDF](https://github.com/parallax/jsPDF) + [jspdf-autotable](https://github.com/simonbengtsson/jsPDF-AutoTable) for the PDF export. The calculation itself is fully offline.

## Licence

© 2026 — All rights reserved. Provided as-is with no warranty. Independently verify every calculation before use on a real engineering project. No permission is granted to redistribute or modify the code without express consent of the owner.
