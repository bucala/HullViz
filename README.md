# ⛵ RC HullViz — RC Boat Draft Calculator

<p align="center">
  <img src="https://img.shields.io/badge/version-2.1.0-1565c0?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/license-MIT-2e7d32?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/single--file-HTML-f57c00?style=flat-square" alt="Single File">
  <img src="https://img.shields.io/badge/no--dependencies-zero-6a1b9a?style=flat-square" alt="No Dependencies">
  <img src="https://img.shields.io/badge/i18n-SK%20%7C%20EN-01696f?style=flat-square" alt="SK/EN">
</p>

<p align="center">
  <strong>Interactive draft calculator for RC boat modellers.</strong><br>
  Enter hull dimensions and component weights — get precise draft in millimetres,<br>
  a live side-view silhouette with waterline, and a real-time Weight/Draft curve.
</p>

<p align="center">
  <a href="#%EF%B8%8F-layout-overview">Layout</a> •
  <a href="#-features">Features</a> •
  <a href="#-physics-model">Physics</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-examples">Examples</a> •
  <a href="#-code-structure">Code Structure</a> •
  <a href="#-audit-notes-v210">Audit</a> •
  <a href="#-changelog">Changelog</a>
</p>

---

## 🗺️ Layout Overview

```
┌─────────────────────┬──────────────────────────────┬─────────────────────┐
│  BOAT PARAMETERS    │  SIDE VIEW CANVAS             │  W / T GRAPH        │
│                     │  (detailed trawler)           │                     │
│  L ──────── 50 cm   │  ⛵ ~~~~~~~~~~~~~~~~~~~~~~~~~│  T ↑                │
│  B ──────── 12 cm   │                               │  │          •       │
│  ρ ── 1.000 g/cm³   │   [mast][wheelhouse][funnel] │  │        ╱         │
│                     │  /──────────────────── WL     │  │      ╱           │
│  WEIGHTS            │  | antifouling  | T = 8.3 mm │  │    ╱             │
│  Hull+servo  480g ▲ │  |______________| [UK flag]  │  └──────────→ W(g)  │
│  Battery     120g ▲ │                               │                     │
│  Motor        60g ▲ │                               │                     │
│  Ballast       0g ▲ │                               │                     │
└─────────────────────┴──────────────────────────────┴─────────────────────┘
┌───────────────┐  ┌──────────────────────┐  ┌──────────────────────────┐
│  DRAFT T      │  │  DISPLACEMENT V      │  │  TOTAL WEIGHT            │
│  8.3 mm       │  │  480.0 cm³           │  │  480 g                   │
└───────────────┘  └──────────────────────┘  └──────────────────────────┘
▼ ACADEMIC SECTION  (click to expand)
   [Concept Map SVG]   [Formula Table]
```

---

## ✨ Features

| Feature | Detail |
|---|---|
| 🌍 **SK / EN toggle** | Language switcher in the header — instant translation of all labels, tooltips, and units |
| 📏 **RC-scale ranges** | Length 8–120 cm, beam 2–30 cm — ranges calibrated for RC models |
| ⚖️ **4 weight components** | Hull+servo · Battery · Motor · Ballast — vertical sliders (visual loading metaphor) |
| 🚢 **Detailed trawler** | Canvas silhouette: hull with sheer line, wheelhouse, funnel, mast, rigging, portholes, UK flag, boat name |
| 🎚️ **Smart sliders** | Horizontal for L, B, ρ — vertical for weights (mimics loading a boat) |
| 💬 **Tooltips** | `ⓘ` icon on every parameter with a physics explanation |
| 📊 **Live W / T chart** | Real-time curve with a red working point and crosshair lines |
| 🎓 **Academic section** | SVG concept map + formula table — collapsible |
| 📱 **Responsive layout** | 3 columns → 2 columns (1140 px) → 1 column (640 px) |

---

## 📐 Physics Model

The calculator implements a **rectangular pontoon** (block coefficient C_B = 1.0):

### Symbols

| Symbol | Quantity | Unit |
|--------|----------|------|
| L | Hull length (LBP) | cm |
| B | Maximum beam | cm |
| W_hull | Hull + servo weight | g |
| W_bat | Battery | g |
| W_motor | Motor | g |
| W_ballast | Ballast (lead weights) | g |
| ρ | Water density | g/cm³ |

### Calculation Steps

```
1.  W_total = W_hull + W_bat + W_motor + W_ballast        [g]
2.  V       = W_total / ρ                                  [cm³]  ← Archimedes' principle
              (g / (g/cm³) = cm³ — no conversion factor needed)
3.  T       = V / (L × B)                                  [cm]   ← Draft
4.  T_mm    = T × 10                                       [mm]   ← Practical RC unit
5.  C_B     = V / (L × B × T) = 1.0                       [–]    ← Block coefficient
```

### Typical Water Densities

| Water type | ρ [g/cm³] |
|-----------|----------|
| Fresh water (lake, pond) | 1.000 |
| Sea water — average | 1.025 |
| Dead Sea | ~1.240 |

---

## 🚀 Quick Start

```bash
git clone https://github.com/bucala/HullViz.git
cd HullViz
open index.html       # macOS
xdg-open index.html   # Linux
start index.html      # Windows
```

**No dependencies. No build step. Single HTML file.**

> Or just download `index.html` and open it directly in any modern browser.

---

## 🔩 Examples

### Small Speedboat (40 cm)

| Component | Weight |
|-----------|--------|
| GFK hull + servo | 200 g |
| LiPo 2S battery | 80 g |
| 540 motor + ESC | 45 g |
| Ballast (lead) | 0 g |
| **Total** | **325 g** |

→ L = 40 cm, B = 10 cm, ρ = 1.000 g/cm³  
→ **T = 325 / (40 × 10) = 0.8125 cm = 8.1 mm**

### Larger Model (80 cm)

| Value | Result |
|-------|--------|
| L = 80 cm, B = 16 cm | |
| W_total = 1 200 g | |
| **Draft T** | **9.4 mm** |

---

## 🗂️ Code Structure

The entire application is a single `index.html` file (~1 200 lines):

```
<style>        Marine light theme · CSS Grid · responsive breakpoints
<script>
  ├─ S                  Global state (lang, L, B, rho, wHull, wBat, wMotor, wBallast)
  ├─ I18N               SK / EN translation objects
  ├─ compute()          Physics: V = W/ρ, T = V/(L·B), T_mm = T·10
  ├─ drawBoat()         Canvas: detailed trawler silhouette with waterline
  ├─ drawGraph()        Canvas: W/T curve with working point
  ├─ updateResults()    Result cards (T, V, W)
  ├─ readState()        Reads DOM values → state object S
  ├─ bindH() / bindV()  Sync horizontal / vertical slider ↔ number input
  ├─ applyLang()        Toggle all texts SK ↔ EN
  └─ updateAll()        Main update dispatcher
```

---

## 🔍 Audit Notes (v2.1.0)

| Area | Status | Notes |
|------|--------|-------|
| Physics | ✅ Correct | Archimedes' principle, direct g/cm³ units, no conversion factor needed |
| i18n | ✅ Complete | All UI strings in both SK and EN, including tooltips and graph labels |
| Canvas rendering | ✅ HiDPI | `devicePixelRatio` scaling applied to both canvases |
| Responsiveness | ✅ 3 breakpoints | 1300 → 1140 → 640 px grid collapse |
| Accessibility | ⚠️ Partial | Sliders lack `aria-label`; canvas lacks `role="img"` + `aria-label` |
| Error handling | ✅ Present | Error banner shown when L or B ≤ 0 |
| Dependencies | ✅ Zero | No external libraries — pure vanilla HTML/CSS/JS |
| Browser support | ✅ Modern | Canvas 2D, CSS Grid, `accent-color` — IE not supported |
| Block coefficient | ℹ️ C_B = 1.0 | Rectangular pontoon assumption — adequate for RC flat-bottom hulls |

---

## 📝 Changelog

### v2.1.0 — 2026-05-22
- **Detailed fishing trawler silhouette** — fully rewritten `drawBoat()` with realistic geometry
- **Hull** — classic sheer line (high bow, low midship, raised stern), deck break, 3-stripe boot-topping
- **Superstructure** — two-storey wheelhouse (4-window cabin + 5-window bridge with wings), radar, funnel
- **Mast & rigging** — tapered mast, spreaders, forestay, backstay, shrouds, derrick boom, nav light
- **Details** — 8 brass portholes, anchor windlass + chain, life ring, UK ensign on stern staff, boat name

### v2.0.0 — 2026-05-22
- Complete rewrite for RC models (8–120 cm range, result in mm)
- New physics — V = W[g] / ρ[g/cm³] (direct RC units, no conversion factor)
- 4 weight components — Hull+servo, Battery, Motor, Ballast
- New RC speedboat canvas visualization

### v1.2.0 — 2026-05-22
- Light theme, unit toggle fix, `user-select` on sliders, independent canvas axes

### v1.1.0 — 2026-05-21
- Overhaul: corvette shape, SK/EN toggle, metric/imperial, smart sliders, academic section

### v1.0.0 — 2026-05-20
- Initial version — basic draft calculator for full-scale vessels

---

## 📖 References

- Barras, C.B. — *Ship Stability for Masters and Mates*, Butterworth-Heinemann
- Eyres, D.J. — *Ship Stability*, Elsevier

---

## 📄 License

MIT — free to use for educational, modelling, and research purposes.

---

<details>
<summary>🇸🇰 Slovenská verzia / Slovak Version</summary>

# ⛵ RC HullViz — Kalkulátor Ponoru RC Loďky

**Interaktívny kalkulátor ponoru pre RC modelárov.**  
Zadáš rozmery trupu (8 cm – 1,2 m) a hmotnosti komponentov — kalkulačka vypočíta presný ponor v milimetroch, vizualizuje siluetu RC loďky s vodnou čiarou a zobrazí závislosť ponoru od celkovej hmotnosti.

## ✨ Funkcie

| Funkcia | Detail |
|---|---|
| 🌍 **SK / EN** | Prepínač jazyka v hlavičke — okamžitá zmena všetkých textov |
| 📏 **RC rozmery** | Dĺžka 8–120 cm, šírka 2–30 cm — rozsah optimalizovaný pre RC modely |
| ⚖️ **4 zložky hmotnosti** | Trup+servo · Batéria · Motor · Balast — vertikálne jazdce (vizuálna metafora nakladania) |
| 🚢 **Vizuál rybárskeho trajlera** | Canvas silueta: trup, kabína, strojovňa, komín, stožiar, ráhna, ilumináty, UK vlajka, meno loďky |
| 🎚️ **Smart jazdce** | Horizontálne pre L, B, ρ · Vertikálne pre hmotnosti (vizuálna metafora nakladania) |
| 💬 **Tooltips** | Ikona `ⓘ` pri každom parametri s fyzikálnym popisom |
| 📊 **Graf W / T** | Live krivka s červeným pracovným bodom a krížovými čiarami |
| 🎓 **Akademická sekcia** | SVG mapa pojmov + tabuľka všetkých vzorcov |
| 📱 **Responzívny dizajn** | 3 stĺpce → 2 stĺpce (1140 px) → 1 stĺpec (640 px) |

## 📐 Fyzikálny model

Kalkulačka implementuje **obdĺžnikový pontón** (blokový koeficient C_B = 1.0):

```
1.  W_total = W_trup + W_bat + W_mot + W_bal               [g]
2.  V       = W_total / ρ                                   [cm³]  ← Archimédov zákon
3.  T       = V / (L × B)                                   [cm]   ← Ponor
4.  T_mm    = T × 10                                        [mm]   ← Praktická jednotka RC
5.  C_B     = V / (L × B × T) = 1.0                        [–]    ← Blokový koeficient
```

### Typické hustoty vody

| Voda | ρ [g/cm³] |
|------|----------|
| Sladká (jazero, rybník) | 1.000 |
| Morská — priemer | 1.025 |
| Mŕtve more | ~1.240 |

## 🚀 Spustenie

```bash
git clone https://github.com/bucala/HullViz.git
cd HullViz
open index.html       # macOS
xdg-open index.html   # Linux
start index.html      # Windows
```

Aplikácia je **single-file HTML** — žiadne závislosti, žiadny build krok.

## 🔩 Príklady pre RC modelárov

### Malý speedboat (40 cm)

| Komponent | Hmotnosť |
|-----------|----------|
| Trup GFK + servo | 200 g |
| LiPo batéria 2S | 80 g |
| Motor 540 + ESC | 45 g |
| Balast (olovené závažia) | 0 g |
| **Celkom** | **325 g** |

→ L = 40 cm, B = 10 cm, ρ = 1.000 g/cm³  
→ **T = 325 / (40 × 10) = 0.8125 cm = 8.1 mm**

### Väčší model (80 cm)

| Veličina | Hodnota |
|----------|---------| 
| L = 80 cm, B = 16 cm | |
| W_total = 1 200 g | |
| **Ponor T** | **9.4 mm** |

## 🔍 Audit (v2.1.0)

| Oblasť | Stav | Poznámka |
|--------|------|----------|
| Fyzika | ✅ Správne | Archimédov zákon, priame jednotky g/cm³ |
| i18n | ✅ Kompletné | Všetky texty v SK aj EN vrátane tooltipov |
| Canvas HiDPI | ✅ | `devicePixelRatio` aplikovaný na oba canvasy |
| Responzívnosť | ✅ 3 body | 1300 → 1140 → 640 px |
| Dostupnosť | ⚠️ Čiastočná | Jazdce bez `aria-label`, canvas bez `role="img"` |
| Error handling | ✅ | Banner pri neplatných vstupoch |
| Závislosti | ✅ Nula | Čisté HTML/CSS/JS |

</details>

---

*Built for the RC modelling community · Bugs and suggestions: open a [GitHub Issue](https://github.com/bucala/HullViz/issues)*
