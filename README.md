# ⛵ RC HullViz — Kalkulátor Ponoru RC Loďky

> **Interaktívny kalkulátor ponoru pre RC modelárov.**  
> Zadáš rozmery trupu (8 cm – 1,2 m) a hmotnosti komponentov — kalkulačka vypočíta presný ponor v milimetroch, vizualizuje siluetu RC loďky s vodnou čiarou a zobrazí závislosť ponoru od celkovej hmotnosti.

---

## 🖼️ Ukážka rozloženia

```
┌────────────────────────┬───────────────────────────┬──────────────────────┐
│  PARAMETRE             │  POHĽAD ZBOKU             │  GRAF W / T          │
│                        │  (RC speedboat)           │                      │
│  L ─────────── 40 cm  │   [radar][flag]           │  T ↑                 │
│  B ─────────── 10 cm  │   ___[cabin]___           │  │         •          │
│  ρ ──────── 1.000 g/cm³│  /   windows  \           │  │       ╱           │
│                        │ /─────────────── WL       │  │     ╱             │
│  Trup+servo  Bat Motor │ |  antifouling  | T=8.3mm │  │   ╱               │
│      ║        ║   ║   │ |_______________| [prop]  │  └──────────→ W (g)  │
│     200g     80g  45g │                            │                      │
└────────────────────────┴───────────────────────────┴──────────────────────┘
  ┌──────────────┐  ┌──────────────────────┐  ┌──────────────────────┐
  │  PONOR T     │  │  OBJEM VÝTLAKU V     │  │  CELKOVÁ HMOTNOSŤ    │
  │  8.3 mm      │  │  325.0 cm³           │  │  325 g               │
  └──────────────┘  └──────────────────────┘  └──────────────────────┘
  ▼ AKADEMICKÁ SEKCIA (kliknutím rozbaliť)
   [Mapa pojmov SVG]   [Tabuľka vzorcov]
```

---

## ✨ Funkcie

| Funkcia | Detail |
|---|---|
| 🌍 **SK / EN** | Prepínač jazyka v hlavičke — okamžitá zmena všetkých textov |
| 📏 **RC rozmery** | Dĺžka 8–120 cm, šírka 2–30 cm — rozsah optimalizovaný pre RC modely |
| ⚖️ **4 zložky hmotnosti** | Trup+servo · Batéria · Motor · Balast — vertikálne jazdce (nakladanie) |
| 🚤 **Vizuál RC speedboat** | Canvas silueta: trup, kabína, okná, stožiar s radarom, vlajka, lodný motor, vrtuľa, bok-topping |
| 🎚️ **Smart jazdce** | Horizontálne pre L, B, ρ · Vertikálne pre hmotnosti (vizuálna metafora nakladania) |
| 💬 **Tooltips** | Ikona `i` pri každom parametri s fyzikálnym popisom |
| 📊 **Graf W / T** | Live krivka s červeným pracovným bodom a krížovými čiarami |
| 🎓 **Akademická sekcia** | SVG mapa pojmov + tabuľka všetkých vzorcov |
| 📱 **Responzívny dizajn** | 3 stĺpce → 2 stĺpce (1140 px) → 1 stĺpec (640 px) |

---

## 📐 Fyzikálny model

Kalkulačka implementuje **obdĺžnikový pontón** (blokový koeficient C_B = 1.0):

### Jednotky

| Symbol | Veličina | Jednotka |
|--------|----------|----------|
| L | Dĺžka trupu (LBP) | cm |
| B | Maximálna šírka | cm |
| W_trup | Hmotnosť trupu + servo | g |
| W_bat | Batéria | g |
| W_motor | Motor | g |
| W_balast | Balast (závažia) | g |
| ρ | Hustota vody | g/cm³ |

### Postup výpočtu

```
1.  W_total = W_trup + W_bat + W_motor + W_balast          [g]
2.  V       = W_total / ρ                                   [cm³]  ← Archimédov zákon
              (g / (g/cm³) = cm³)
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

---

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
| L | 80 cm |
| B | 16 cm |
| W_total | 1 200 g |
| **T** | **9.4 mm** |

---

## 🗂️ Štruktúra kódu (`index.html`)

```
<style>        Svetlý námorný dizajn · CSS Grid · responzívne body
<script>
  ├─ S                  Globálny stav (lang, L, B, rho, wHull, wBat, wMotor, wBallast)
  ├─ I18N               SK/EN prekladové objekty
  ├─ compute()          Fyzika: V = W/ρ, T = V/(L·B), T_mm = T·10
  ├─ drawBoat()         Canvas: RC speedboat silueta s vodnou čiarou
  ├─ drawGraph()        Canvas: krivka W/T s pracovným bodom
  ├─ updateResults()    Výsledkové karty (T, V, W)
  ├─ readState()        Čítanie hodnôt z DOM → S
  ├─ bindH() / bindV()  Sync horizontálny / vertikálny jazdec ↔ číslo
  ├─ applyLang()        Prepnutie všetkých textov SK↔EN
  └─ updateAll()        Hlavná update funkcia
```

---

## 🚀 Spustenie

```bash
git clone https://github.com/bucala/HullViz.git
cd HullViz
open index.html       # macOS
xdg-open index.html   # Linux
start index.html      # Windows
```

Aplikácia je **single-file HTML** — žiadne závislosti, žiadny build krok.

---

## 📝 Changelog

### v2.0.0 — 2026-05-22
- **Kompletný prepis pre RC modely** — rozsah 8–120 cm dĺžka, výsledok v milimetroch
- **Nová fyzika** — V = W[g] / ρ[g/cm³] = cm³ (priame RC jednotky, bez konverzného faktora)
- **4 zložky hmotnosti** — Trup+servo, Batéria, Motor, Balast (namiesto reálnej lode)
- **Nová vizualizácia RC speedboat** — presnejší model s kabínou, oknami, radarom, lodným motorom a vrtuľou
- **Aktualizovaný README** — príklady pre RC modelárov, tabuľky komponentov

### v1.2.0 — 2026-05-22
- Svetlý režim (light theme), oprava unit toggle, user-select na slidery, nezávislé X/Y osi canvas

### v1.1.0 — 2026-05-21
- Overhaul: korvetka, SK/EN, metrické/imperiálne, smart jazdce, akademická sekcia

### v1.0.0 — 2026-05-20
- Prvá verzia — základný kalkulátor ponoru pre reálne lode

---

## 📖 Literatúra

- Barras, C.B. — *Ship Stability for Masters and Mates*, Butterworth-Heinemann
- Eyres, D.J. — *Ship Stability*, Elsevier

---

## 📄 Licencia

MIT — voľné použitie pre vzdelávacie, modelárske a výskumné účely.

---

*Vytvorené pre komunitu RC modelárov · Chyby a návrhy: otvorte GitHub Issue*
