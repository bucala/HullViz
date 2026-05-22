# ⚓ HullViz — Kalkulátor Ponoru Lode

> **Interaktívny kalkulátor ponoru pre modelárov a záujemcov o námornú hydrodynamiku.**  
> Vypočíta presný ponor podľa Archimédovho zákona, vizualizuje polohu vodovody na trup vojnovej lode a zobrazí závislosť ponoru od záťaže.

---

## 🖼️ Ukážka rozloženia

```
┌─────────────────────┬──────────────────────────┬────────────────────┐
│  PARAMETRE          │  POHĽAD ZBOKU             │  GRAF M / T        │
│                     │  (vojnová loď)            │                    │
│  L ──────── 50 m    │   [mast+radar]            │  T ↑               │
│  B ──────── 10 m    │   |  [bridge][funnel]     │  │        •        │
│                     │  _|________________       │  │      ╱          │
│  W_ship   W_cargo   │  |   main deck    |       │  │    ╱            │
│   ║          ║      │ ═════════════════ WL      │  │  ╱              │
│  1000t      500t    │  |  submerged     |T=1.95m│  └──────────→ M    │
│                     │  |_______________|        │                    │
│  ρ ──── 1025 kg/m³  │                           │                    │
└─────────────────────┴──────────────────────────┴────────────────────┘
  ┌──────────────┐  ┌──────────────────────┐  ┌──────────────────────┐
  │  PONOR T     │  │  OBJEM VÝTLAKU V     │  │  CELKOVÁ HMOTNOSŤ    │
  │  1.951 m     │  │  975.6 m³            │  │  1500.0 t            │
  └──────────────┘  └──────────────────────┘  └──────────────────────┘
  ▼ AKADEMICKÁ SEKCIA (kliknutím rozbaliť)
   [Mapa pojmov SVG]   [Tabuľka vzorcov]
```

---

## ✨ Funkcie

| Funkcia | Detail |
|---|---|
| 🌍 **SK / EN** | Prepínač jazyka v hlavičke — okamžitá zmena všetkých textov |
| 📏 **Metrické / Imperiálne** | m ↔ ft · t ↔ LT (long ton) · kg/m³ ↔ lb/ft³ · m³ ↔ ft³ |
| 🚢 **Vizualizácia vojnovej lode** | Silueta korvetty na canvas: trup, mostík, kanón, stožiar s radarom, komín, iluminátory, vodorovná značka WL |
| 🎚️ **Smart smerové ovládače** | **Horizontálne jazdce** pre L a B (dĺžkové rozmery ↔) · **Vertikálne jazdce** pre záťaž (vizuálna metafora nakladania ↕) |
| 💬 **Kontextové vysvetlivky** | Hover tooltip `i` pri každom parametri — fyzikálny opis, typické hodnoty, vplyv na ponor |
| 📊 **Graf M / T** | Live line chart, červený krúžok = aktuálny pracovný bod s krížovými čiarami a popisom |
| 🎓 **Akademická sekcia** | Rozbaľovacia: SVG mapa pojmov + kompletná tabuľka vzorcov pre ručné overenie |
| ⚠️ **Validácia** | Chybové bannery, varovanie ak ponor presahuje dĺžku lode alebo 50 m |
| 📱 **Responzívny dizajn** | 3 stĺpce (wide) → 2 stĺpce (1200 px) → 1 stĺpec (700 px) |

---

## 📐 Fyzikálny model

Kalkulačka implementuje **obdĺžnikový pontón** (blokový koeficient C_B = 1.0):

```
Výtlakový objem:   V(T) = L × B × T
```

### Vstupné veličiny

| Symbol | Veličina | Metrické | Imperiálne |
|--------|----------|----------|-----------|
| L | Dĺžka lode (LBP) | m | ft |
| B | Šírka trupu | m | ft |
| W_ship | Vlastná hmotnosť (lightship) | t | LT |
| W_cargo | Náklad + zásoby + palivo | t | LT |
| ρ | Hustota vody | kg/m³ | lb/ft³ |

### Postup výpočtu

```
1.  W_total = W_ship + W_cargo                   [t]
2.  W_kg    = W_total × 1000                     [kg]
3.  V       = W_kg / ρ                            [m³]  ← Archimédov zákon
4.  T       = V / (L × B)                         [m]   ← Ponor
              alebo skrátene:
    T       = W_kg / (ρ × L × B)

5.  F_A     = ρ × g × V = W_kg × g               [N]   ← Vztlaková sila
    C_B     = V / (L × B × T) = 1.0              [-]   ← Blokový koef.
```

### Typické hustoty vody

| Voda | ρ [kg/m³] |
|------|----------|
| Sladká (jazerá, rieky) | 1 000 |
| Morská — priemer | 1 025 |
| Teplá morská | ~1 020 |
| Studená morská | ~1 028 |
| Mŕtve more | ~1 240 |

---

## 🔩 Pre modelárov RC lodí

Kalkulačka bola navrhnutá špeciálne na to, aby **RC modely mali rovnaký relatívny ponor ako reálne predlohy**.

### Škálovanie

Hmotnosť sa škáluje **kubicky** (objem ∝ mierka³):

```
L_model = L_real / k
B_model = B_real / k
W_model = W_real / k³
T_model = T_real / k         (ponor sa škáluje lineárne)
```

**Príklad — torpédoborec v mierke 1 : 100:**

| Veličina | Reálna loď | Model 1:100 |
|----------|-----------|------------|
| L | 120 m | 1.20 m |
| B | 13 m | 0.13 m |
| W_total | 3 500 t | 3.5 g |
| T | 4.2 m | 42 mm |

> Nastavte tieto hodnoty do kalkulačky — výsledný ponor modelu by mal byť **42 mm**, čo v mierke zodpovedá skutočným **4.2 m** ✓

---

## 🎓 Akademická sekcia

Kliknite na **▼ Akademická sekcia** priamo v aplikácii. Zobrazí sa:

### Mapa pojmov (SVG diagram)

```
[W_ship] ──┐
            ├─→ [W_total] ──→ [V = W_kg / ρ] ──→ [T]
[W_cargo] ──┘         ↑                                ↑
                      │                                │
                     [ρ]                           [L × B]
```

### Tabuľka vzorcov

Každý krok výpočtu je vypísaný samostatne s jednotkami a popisom — vhodné na ručné overenie kalkulačky alebo na cvičenie v škole.

---

## 🚀 Spustenie

```bash
git clone https://github.com/bucala/HullViz.git
cd HullViz
# Otvorte priamo — žiadny build krok nie je potrebný
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

Aplikácia je **single-file HTML** bez akýchkoľvek externých závislostí alebo build nástrojov.

---

## 🗂️ Štruktúra kódu (`index.html`)

```
<style>        Dark naval theme · CSS Grid · responsive breakpoints
<script>
  ├─ STATE        Globálny stav: jazyk, jednotky, metrické vstupné hodnoty
  ├─ I18N         SK/EN prekladové objekty
  ├─ UNIT_DEF     Prevodné koeficienty (m↔ft, t↔LT, …)
  ├─ getInputs()          Čítanie a validácia vstupov + prevod na SI
  ├─ computeDraft()       Fyzikálny výpočet T, V, W_total
  ├─ computeCurve()       Body pre krivku M/T
  ├─ updateResults()      Aktualizácia výsledkových kariet
  ├─ drawShipPreview()    Canvas: silueta vojnovej lode s vodovúdou
  ├─ drawGraph()          Canvas: line chart M/T
  ├─ applyTranslations()  Prepnutie všetkých textov
  ├─ applyUnits()         Konverzia hodnôt pri zmene sústav
  ├─ bindHSlider()        Sync horizontálny jazdec ↔ číslo
  ├─ bindVSlider()        Sync vertikálny jazdec ↔ číslo
  ├─ setupEvents()        Registrácia event listenerov
  └─ updateAll()          Hlavná update funkcia (volaná pri každej zmene)
```

---

## 📝 Changelog

### v1.2.0 — 2026-05-22
- **Svetlý režim** — kompletný prepis na light theme (`#f0f4f8` pozadie, biele karty, modrý akcent `#1565c0`); tmavo-námorná hlavička zachovaná
- **Oprava prepínača jednotiek** — tlačidlá Metric / Imperial teraz správne zvýrazňujú aktívny stav pri každom prepnutí aj pri štarte
- **Oprava sliderov** — pridaný `user-select: none` na všetky obalové elementy sliderov; ťahanie jazdca už nevyberá okolný text
- **Oprava proporcií lode** — nezávislé osi X / Y na canvas; loď teraz vždy vypĺňa výšku plátna bez ohľadu na pomer L/T (napr. 650:1)

### v1.1.0 — 2026-05-21
- **Vizuál vojnovej lode** — silueta korvetty na canvas (trup, mostík, komín, stožiar s radarom, kanón, ilumináty, antifouling)
- **SK / EN prepínač** — okamžitá zmena všetkých textov cez I18N objekty
- **Metrické / Imperiálne jednotky** — m ↔ ft, t ↔ LT, kg/m³ ↔ lb/ft³, m³ ↔ ft³; interná sústava vždy metrická
- **Smart jazdce** — horizontálne pre L a B, vertikálne pre W_ship a W_cargo (vizuálna metafora nakladania)
- **Akademická sekcia** — rozbaľovacia: SVG mapa pojmov + tabuľka všetkých vzorcov
- **Kontextové tooltips** — ikona `i` pri každom parametri s fyzikálnym popisom a typickými hodnotami
- **README** — vizuálne atraktívny popis projektu s ASCII layoutom, škálovacím sprievodcom pre RC modelárov a fyzikálnym modelom

### v1.0.0 — 2026-05-20
- Prvá verzia — jednoduchý kalkulátor ponoru (Archimédov zákon, `T = W_kg / (ρ × L × B)`)
- Canvas vizualizácia trupu a graf M / T
- Responzívny layout (3 stĺpce → 2 → 1)

---

## 📖 Literatúra

- Barras, C.B. — *Ship Stability for Masters and Mates*, Butterworth-Heinemann
- Eyres, D.J. — *Ship Stability*, Elsevier
- SOLAS Chapter II-1 — Subdivision and damage stability

---

## 📄 Licencia

MIT — voľné použitie pre vzdelávacie, modelárske a výskumné účely.

---

*Vytvorené pre komunitu lodných modelárov · Chyby a návrhy: otvorte GitHub Issue*
