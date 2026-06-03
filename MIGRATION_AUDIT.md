# Japanese → Chinese Travel Fixture Migration Audit

**Date:** 2026-06-02  
**Scope:** [`index.html`](index.html) (sole runtime file)  
**Currency:** `¥` symbols left unchanged per product decision.

## Summary

| Metric | Value |
|--------|-------|
| Files modified | 1 (`index.html`) |
| Audit report | `MIGRATION_AUDIT.md` |
| Japanese city/airport fixture hits remaining | 0 |
| Schema keys preserved | `data-*` IDs, `value="G"`, `jr`/`air` mode keys, `transType` values |
| Exclusions preserved | `NGO/THI THANH` (person name), person names (TANAKA/YUKI, etc.) |
| `handleSearch` / form hooks | Unchanged |

## Verification Results

| Check | Result |
|-------|--------|
| `Tokyo\|Osaka\|Kyoto\|Shinkansen\|Narita\|Haneda\|Hiroshima` in `index.html` | 0 matches |
| `NGO/THI THANH` | Unchanged (2 rows) |
| Rail filter `value="G"` | Unchanged |
| `FLIGHT_AIRPORTS` schema (`iata`, `city`, `airport`) | Intact; HGH added |
| Airport combo dropdown | 3-line format (code / city / airport) |
| Airport combo closed state | 3-line format added |
| `airlineMap` legacy keys (`JAL`, `ANA`, `Spring Japan`) | Retained for backward compatibility |

---

## Migration Table

| File Path | Original Japanese Value | New Chinese Value | Reason for Replacement |
| :--- | :--- | :--- | :--- |
| `index.html` | `FLIGHT_AIRPORTS` (5 entries) | Added `HGH` — Hangzhou Xiaoshan International Airport | Localization Strategy Update |
| `index.html` | Airport combo closed state (2 lines) | 3 lines: code + city + full airport name | Metadata Restructuring Rule (Task 2) |
| `index.html` | `.flight-airport-combo__city` only in closed picker | Added `.flight-airport-combo__airport` CSS | Metadata Restructuring Rule (Task 2) |
| `index.html` | `Conference — Tokyo (return via Osaka)` | `Conference — Beijing (return via Shanghai)` | City mapping (Tokyo→Beijing, Osaka→Shanghai) |
| `index.html` | Rail default `Tokyo Station` | `Beijing South Station` | City mapping |
| `index.html` | Rail default `Shin-Osaka` | `Shanghai Hongqiao` | City mapping |
| `index.html` | `G — Shinkansen` (filter label) | `G — China High-Speed Rail (CRH)` | Railway taxonomy (`value="G"` unchanged) |
| `index.html` | Hotel default `Tokyo` | `Beijing` | City mapping |
| `index.html` | Car pickup `Narita Airport T1` | `PEK Terminal 3` | Airport mapping (NRT→PEK) |
| `index.html` | Car dropoff `Shinagawa Station` | `Beijing South Railway Station` | Geographic fixture |
| `index.html` | `BCD Travel Japan` | `BCD Travel China` | Geographic label |
| `index.html` | Library route `Shanghai → Tokyo` | `Shanghai → Beijing` | City mapping |
| `index.html` | Offline form label `JR route` | `Rail route` | Railway taxonomy |
| `index.html` | Reservation flight summary `NRT (Narita) → SIN` / carrier `NH` | `PEK (Capital) → SIN` / carrier `CA` | Airport + carrier mapping |
| `index.html` | Reservation train summary `Tokyo → Osaka` / `Shinkansen` | `Beijing → Shanghai` / `China High-Speed Rail (CRH)` | City + railway mapping |
| `index.html` | `airlines` mock array `JAL`, `ANA`, `Spring Japan` | `Air China`, `China Eastern Airlines`, `Hainan Airlines`, `China Southern Airlines` | Carrier mapping |
| `index.html` | `carriers` mock array `JAL`, `ANA` | `Air China`, `China Eastern Airlines` | Carrier mapping |
| `index.html` | `airlineMap` (2 sites) | Extended with `Air China: CA`, `China Eastern Airlines: MU`, `China Southern Airlines: CZ`, `Hainan Airlines: HU`; legacy keys kept | Carrier mapping + backward compat |
| `index.html` | `depStations` / `arrStations` Japanese hub names | Beijing, Beijing South, Shenzhen North, Guangzhou South, Shanghai Hongqiao, Hangzhou East, Chengdu East | City mapping |
| `index.html` | `_buildMockOnlineRail` services `Nozomi`, `Hikari`, `Kodama`, etc. | `G28`, `G1`, `D24`, `G848`, `D30`, `D38` (CRH-style) | Railway taxonomy |
| `index.html` | `_buildMockOnlineHotels` (12 Tokyo-area hotels) | 12 Beijing/Shanghai-area hotels with CN coordinates | City mapping (12 entries updated) |
| `index.html` | `ROUTE_DATA` (5 routes, ~35 segments) | Full Chinese domestic routes (Chengdu/PEK/Guangzhou/Shenzhen/Hangzhou hubs, CRH/Air China) | Localization Strategy Update |
| `index.html` | Route planner badge `Shinkansen` | `China High-Speed Rail (CRH)` | Railway taxonomy |
| `index.html` | Itinerary title `Shinkansen (JR)` | `China High-Speed Rail (CRH)` | Railway taxonomy + badge key sync |
| `index.html` | Itinerary title `Shinkansen (JR) (Return)` | `China High-Speed Rail (CRH) (Return)` | Railway taxonomy + badge key sync |
| `index.html` | Itinerary title `Railway (JR)` | `China Railway` | Railway taxonomy + badge key sync |
| `index.html` | Itinerary title `Railway (JR) (Return)` / `Railway (Return)` | `China Railway (Return)` | Railway taxonomy + badge key sync |
| `index.html` | `TYPE_BADGE` / `iconColor` keys | Renamed to match new itinerary titles | Presentation key coupling (zero-regression) |
| `index.html` | Offline JR item title `Railway (JR)` | `China Railway` | Railway taxonomy |
| `index.html` | Online booking meta `jr.title` `Railway (JR)` | `China Railway` | Railway taxonomy |
| `index.html` | `segmentToItem` bullet/rail titles | `China High-Speed Rail (CRH)` / `China Railway` | Railway taxonomy |
| `index.html` | Edit-path check `Railway (Return)` | `China Railway (Return)` | Logic string sync (regex `(return)` still applies) |

---

## Intentionally Unchanged

| Item | Reason |
|------|--------|
| `NGO/THI THANH` | Person name, not Nagoya airport |
| `TANAKA/YUKI`, `NANASHINO GONBE`, etc. | Person names, not geography |
| `airlineMap` keys `JAL`, `ANA`, `Spring Japan` | Structural property keys for legacy demo rows |
| `value="G"` on rail filter checkbox | Schema / filter gate |
| `jr`, `air`, `hotel`, `car` meta object keys | Structural mode keys |
| `transType` values `rail`, `bus`, `ship` | Schema values |
| All `¥` fare amounts and symbols | Product decision |
| Tailwind classes, spacing, icons | Task 3 constraint |
| `handleSearch`, `onkeydown`, form submission hooks | Task 3 constraint |

---

## Manual Smoke Checklist

- [ ] Dashboard approvals row shows Beijing/Shanghai conference text
- [ ] Library trip row shows `Shanghai → Beijing`
- [ ] Booking → Flight airport combo: closed + open show code / city / airport (3 lines)
- [ ] Booking → Rail defaults show Beijing South / Shanghai Hongqiao
- [ ] Booking → Hotel default city is Beijing
- [ ] Booking → Car defaults show PEK Terminal 3 / Beijing South Railway Station
- [ ] Online rail results use Chinese station names and CRH-style train codes
- [ ] Route planner (`ROUTE_DATA`) shows Chinese segments and CRH badges
- [ ] Itinerary cards show correct badge colors for `China Railway` / `China High-Speed Rail (CRH)` titles
