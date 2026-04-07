# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

A collection of standalone HTML tools for Geek2Go computer repair technicians. Each tool is a self-contained application running entirely in the browser with no build process or external dependencies.

**Live site**: https://geek2go.github.io/geek2go-tools/

## Architecture

All tools use vanilla JavaScript (IIFE scoping), embedded CSS, and zero frameworks.

- **Shared CSS** (`shared.css`): Common buttons, forms, cards, grid layouts
- **Tool CSS**: Theme variables and unique styles embedded per file
- **State**: DOM state + localStorage (no external libraries)
- **UI Pattern**: Two-column layout — form/inputs left, live output right
- **Data flow**: User input → JS processing → formatted text for copy/paste into POS (MyRepairApp)

---

## Files

### index.html
Landing page linking to all tools.

---

### repair-checklist.html
Interactive repair checklist generator for phones, tablets, and game consoles.

- Device-specific Pre/Post checklists driven by a `TEMPLATES` object
- Live output updates without a "generate" button
- Output: plain text with `[x]` / `[ ]` checkbox notation for ticket systems

---

### pc-doctor-summarizer.html
Documents hardware diagnostics and service work with PC-Doctor report parsing.

- Parsers: `parseCPU()`, `parseRAM()`, `parseStorage()`, `parseGPU()`, `parseOS()`, `parseTests()`
- Preset workflows: Quick Clean, New PC Setup, Business Standard, Gaming Build, Managed Care
- Failure detection avoids false positives (checks for `Failed`, `Error`, `Critical`, `Bad`, `SMART Warning`)
- Defaults saved/loaded via localStorage

---

### pricing-calculator.html
**Main active tool.** Multi-tab pricing calculator for tech bench use.

#### Tabs

| Tab | Status | Description |
|---|---|---|
| iPhone Presets | Active | Preset pricing for iPhone 6 through 17 series |
| Android Presets | WIP | Samsung Galaxy S20–S26 Ultra + Galaxy A series |
| Manual Calculator | Active | Custom labor + parts quotes for any device |
| Console Repair | Active | PS5, PS4, Xbox Series/One, Nintendo Switch |

---

#### iPhone Presets

**Data**: `IPHONE_PRICING` — all models keyed by name, repair type → price.
**Screen repairs**: Soft OLED AM only (no OEM option).
**DisplayShield**: Auto-included on all screen repairs. Cost is built into the screen price — nothing added to total. Deductible auto-selected per model from `IPHONE_DS_DEDUCTIBLE`.
**Secondary repairs**: 40% off (Battery flat $60, Back Glass no discount).
**POS SKUs**: `IPHONE_LABOR_SKUS` maps repair type → labor SKU. SKU panel shown in right column when repair is selected. Reminder to attach part SKU in POS.

**Updating screen prices**: Edit `IPHONE_PRICING[model]["Screen (Soft OLED AM)"]`.
**Updating deductibles**: Edit `IPHONE_DS_DEDUCTIBLE[model]` (values: 0, 29, 49, 99).
**Adding a new model**: Add entry to `IPHONE_PRICING` and `IPHONE_DS_DEDUCTIBLE`. Model order follows CSV (newest → oldest).

**Reference file**: `POS Price Reference.csv` — source of truth for screen prices and deductible tiers.

---

#### Android Presets (WIP)

**Status**: Structure and models are in place. All prices are `null` (TBD).

**Models**:
- Samsung Galaxy S Series: S20 through S26 Ultra (26 models)
- Samsung Galaxy A Series: A02s, A03s, A10, A10e, A12, A13 5G, A14 5G, A15 5G, A32 5G, A51 5G, A52 5G

**Repair types**: Screen Repair, Battery, Charging Port, Back Cover / Glass.
**DisplayShield**: Auto-shown on screen repairs. Deductible selector present but tiers are TBD per model.
**POS SKUs**: Auto-selected based on series — `SMSCREENREP` etc. for S-series, `ASERIES-SCREEN` etc. for A-series.

**When pricing is ready**:
1. Fill in `null` values in `ANDROID_PRICING[model][repairType]`
2. Add `ANDROID_DS_DEDUCTIBLE` object (same pattern as `IPHONE_DS_DEDUCTIBLE`)
3. Wire deductible auto-select into `calculateAndroid()` (same pattern as `calculateIphone()`)
4. Remove WIP badge from tab and WIP notice card from mode content

---

#### Manual Calculator

For non-preset devices (Samsung, Motorola, Pixel, Android, iPad, etc.).

- **Labor tiers**: $80 Budget / $120 Mid-range / $150 Flagship / Custom
- **Parts**: Add by name + cost, markup slider (10–20%)
- **Shipping**: None / $10 2-day / $25 overnight
- **DisplayShield**: Optional toggle. Shows exact store cost + rounded customer charge. Store cost from `DS_COSTS` (lower-bound lookup by repair total, per deductible tier). Source: `DisplayShield Costs.csv`.
- **POS SKU Reference**: Brand selector (Samsung Galaxy S/A, Motorola, Google Pixel, Android Other) + repair type → builds a SKU list. Data in `MANUAL_SKUS`. Notes: link part item, 40% secondary discount, missing SKU warning.

**Updating DS costs**: Edit `DS_COSTS` in the script. Rows are `[minRepairPrice, storeCost]` lower-bound pairs. Reference: `DisplayShield Costs.csv`.

---

#### Console Repair

Flat-rate pricing for game consoles.

- **Current Gen** ($175 flat): PS5, Xbox Series X/S
- **Last Gen** ($150 flat): PS4 Pro, PS4/Slim, Xbox One S/X, Xbox One
- **Nintendo Switch**: Per-repair pricing (Switch, Lite, OLED, Switch 2)
- Repair types: HDMI/No Display, No Power, Hardware (Fan/Disc Drive), Software Issue, Cleaning/Overheating. Switch adds: Digitizer, LCD, Battery, Charge Port, Card Reader, Joystick/Rail.
- POS SKU shown on repair selection.
- Tech Notes card: $75 diag fee, liquid damage → ADVREPAIR, parts billed separately, retro consoles case-by-case.

**Data**: `CONSOLE_PRICING[device].repairs[repairType]` → `{ price, sku }`.

---

#### DisplayShield Logic

- **iPhone presets**: DS cost is baked into screen price. `dsAdd = 0`. Deductible shown for reference only. Auto-selected from `IPHONE_DS_DEDUCTIBLE`.
- **Android presets**: Same intent as iPhone once pricing is built out.
- **Manual calculator**: DS is optional. Store cost = `getDSStoreCost(repairTotal, deductible)`. Customer charge = `getDSCharge()` (rounds up to nearest $10). Both shown in DS card.

`getDSStoreCost()` uses lower-bound lookup: finds highest `minPrice ≤ repairTotal` in `DS_COSTS[deductible]`.

---

#### Right Column (shared output)

- **Total display**: Updates live per mode
- **POS Labor SKUs panel** (`#sku-section`): Shows labor SKU per repair. Visible in iPhone and Android modes when a repair is selected. Hidden in Manual and Console modes.
- **SMS Quote**: Copy-ready customer message
- **Detailed Breakdown**: Copy-ready breakdown for ticket notes

---

### Bitdefender Mobile Manager (Google Apps Script)

Hosted separately at script.google.com. Manages Bitdefender Mobile Security subscriptions, tracks renewals, sends activation emails via Gmail. Not version-controlled here. Linked from index.html.

---

## Data Files (not committed to repo — reference only)

| File | Purpose |
|---|---|
| `POS Price Reference.csv` | iPhone screen prices + DS deductible tiers per model |
| `DisplayShield Costs.csv` | DS store costs by repair price range and deductible tier |
| `Samsung_Android SKU CS.csv` | Samsung/Android labor SKUs and notes |
| `G2G_Game_Console_Repair_Pricing.xlsx` | Console flat-rate pricing and SKU reference |

---

## Branding

- **Accent red**: `#C10A11`
- **Background**: `#f9f9f9`
- **Cards**: `#ffffff`
- **Text**: `#000000`
- **Muted**: `#6b7280`
- **Status amber**: `#F59E0B` (WIP badges, warnings)
- **Status green**: `#10B981`

---

## Common Tasks

**Add a new iPhone model**: Add to `IPHONE_PRICING` and `IPHONE_DS_DEDUCTIBLE`. Follow newest→oldest order. Screen (Soft OLED AM) only.

**Update a screen price**: Edit `IPHONE_PRICING[model]["Screen (Soft OLED AM)"]`. Use `POS Price Reference.csv` as source of truth.

**Add a console**: Add entry to `CONSOLE_PRICING` with repairs object. Add model to the appropriate `CONSOLE_GROUPS` entry.

**Add an Android model**: Add to `ANDROID_MODELS.S` or `ANDROID_MODELS.A` array. `ANDROID_PRICING` is auto-built from those arrays with `null` prices.

**Update DS cost table**: Edit `DS_COSTS` using `DisplayShield Costs.csv`. Structure: `{ deductible: [[minPrice, storeCost], ...] }`.

**Deploy**: `git push` to main. GitHub Pages auto-deploys. Allow 2–5 minutes.
