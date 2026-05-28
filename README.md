# OBT China — Responsive refactor notes

## UI refactor map — mobile login glass, logos, dashboard 3-up (May 2026)

Four **mobile-only** (`<768px`) refactors. Desktop (`md:`+) unchanged.

| Task | Target | Mobile change | Desktop guard |
| --- | --- | --- | --- |
| **1 — Transparent login panel** | `.login-aside` | `bg-white/10 backdrop-blur-md border-white/20`; inputs `bg-white/96`; light text + shadow on labels | `md:bg-white` full column |
| **2 — Login logo layering** | Login header | Removed mobile watermark; `max-md:hidden` on top-right preview logo; `blue.png` in `.login-mobile-brand` with `z-10` | Hero + corner logos unchanged at `md:`+ |
| **3 — In-app header logo** | `#main-content-wrapper` mobile row | `blue.png` replaces `tripsource-logo.png` in hamburger header | Sidebar keeps `tripsource-logo-removebg-preview.png` |
| **4 — Dashboard 3-up counters** | `#page-dashboard` stat grids | `grid-cols-3 gap-2`, compact chips + `text-xl` values via `.dashboard-stat-*` | `md:gap-4`, `md:p-6`, `md:text-3xl`, `md:p-4` on chips |

---

## UI refactor map — login, dashboard clusters, low-content pages (May 2026)

Four scoped layout refactors in **`OBT China.html`**. Desktop (`md:` / 768px+) layouts outside the noted selectors are unchanged.

| Task | Target | Mobile / tablet behavior | Desktop guard (`md:`+) |
| --- | --- | --- | --- |
| **1 — Login branding** | Hero + mobile sticky bar logos | `blue.png` with `object-contain`, explicit `width`/`height` | Same asset swap; hero layout unchanged |
| **2 — Login backdrop** | `.login-bg-photo`, `.login-bg-overlay`, `.login-aside` | `fixed inset-0 w-full h-full bg-cover bg-center bg-no-repeat`; glass card (`bg-white/92`, `backdrop-blur-md`) centered over photo | `md:absolute` background; full-height white `login-aside` column |
| **3 — Dashboard stat rows** | Your Reservation + Approver Menu inner grids | `grid-cols-1` below 450px; `min-[450px]:grid-cols-3` for tablet (e.g. Tab S8 Ultra portrait) | `md:grid-cols-3` — identical 3-up row |
| **4 — Low-content page gap** | `#page-library`, `#page-reports`, `#page-user-management` | `max-md:flex-none`, reduced header `pt`/`mb`, CSS `flex: 0 0 auto` + `min-height: 0` on scroll wrappers | `min-h-screen`, `md:mb-8`, original padding |

### Task 1 — Login logo asset

| Location | Selector / lines | Asset |
| --- | --- | --- |
| Desktop hero (photo side) | `#login-screen .hidden.md:flex` top-left `img` | `blue.png` |
| Mobile sticky bar | `.login-aside` sticky row `img.logo-on-light` | `blue.png` |

Decorative watermarks and top-right preview logo remain `tripsource-logo-removebg-preview.png`. Post-login header/sidebar logos unchanged.

### Task 2 — Login mobile background + glass form

| Layer | Classes / CSS |
| --- | --- |
| Photo | `.login-bg-photo` + `fixed md:absolute inset-0 w-full h-full bg-cover bg-center bg-no-repeat` |
| Scrim | `.login-bg-overlay` + `fixed md:absolute`; lighter gradient in `@media (max-width: 767px)` |
| Form shell | `.login-aside` + `max-md:rounded-2xl max-md:bg-white/92 max-md:backdrop-blur-md max-md:shadow-2xl`; `#login-screen` centers with `max-md:items-center max-md:justify-center max-md:p-4` |
| Scroll area | `.login-panel-scroll` + `max-md:min-h-0 max-md:justify-start max-md:pt-4` |

### Task 3 — Dashboard tracking clusters (tablet side-by-side)

| Section | Inner grid classes |
| --- | --- |
| Your Reservation | `grid grid-cols-1 min-[450px]:grid-cols-3 gap-4 items-stretch w-full text-center md:grid-cols-3` |
| Approver Menu | Same |

Stat tiles: `p-3 min-[450px]:p-4 md:p-4` for compact tablet height; equal-width columns from 450px up.

### Task 4 — Library / Reports / User Management mobile whitespace

| Fix | Implementation |
| --- | --- |
| Drop viewport stretch | `max-md:flex-none` on `<main>`; CSS `flex: 0 0 auto !important` + `min-height: 0` |
| Tighter header band | First child: `max-md:pt-3 max-md:pb-0`; title row `mb-2 max-md:mb-3 md:mb-8` |
| Content flush below title | Second child `pt-0`; CSS zeros extra top padding on `.flex-1` body wrapper |

---

## UI refactor map (May 2026)

Five scoped UI updates in **`OBT China.html`** (May 2026). Search/filter/session logic is unchanged; desktop layouts outside the filter results column are preserved at **`md:` (768px)+**.

| Task | DOM anchors | Breakpoint classes | JS hooks |
| --- | --- | --- | --- |
| Stepper | `#booking-steps-indicator`, `#change-request-steps-indicator` | `.booking-stepper-card`; vertical labels; `sm:` horizontal row | `setBookingStep`, `setChangeRequestStep` |
| Filter drawer | `#obt-filter-drawer-backdrop`, `#obt-filter-drawer-panel`, `#obt-filter-drawer-body`, `#obt-filter-mount-{flight,hotel,rail}`, `#obt-filter-open-*` | `< md` only: `hidden md:hidden` backdrop, `md:hidden` panel, bottom sheet (`translateY` + `body.obt-filter-drawer-open`); `≥ md`: in-flow sidebars (`#online-*-filter-sidebar`, `lg:pl-[276px]`) | `openObtFilterDrawer`, `closeObtFilterDrawer`, `applyObtFilterDrawer`, `resetObtFilterDrawer` |
| Passport modal | `#passport-notice-modal`, `.passport-notice-modal-panel` | `max-h-[90vh]`, `overflow-y-auto` on inner panel only | `togglePassportNoticeModal` |
| Flight cards | `#online-flight-cards-going`, `#online-flight-cards-return`, `#online-flight-tbody-*` | `max-md:` card list + mobile sort in results header; `md:` table + thead | `_renderOnlineFlightCardRowsHtml`, `_renderOnlineFlightTableRowsHtml`, `refreshOnlineFlightRowBodies` |
| Current Itinerary | `#itinerary-summary-horizontal`, `#itinerary-active-leg-line`, `#itinerary-embedded-details` | `whitespace-nowrap truncate` on trip line; See List `ml-auto` in header row | `renderItinerarySummary`, `buildItineraryCompactSummaryHtml`, `openItineraryListModal` |

**Filter drawer note:** Flights, Hotels, and Rail each use the shared drawer; filter markup is teleported into `#obt-filter-drawer-body` on open and restored on close so existing `getElementById` filter IDs keep working. Legacy flight-only mobile drawer CSS (`body.flight-filter-drawer-open`) is superseded by `body.obt-filter-drawer-open`.

**Prior mobile work:** See [`MOBILE_RESPONSIVE_CHANGES.md`](MOBILE_RESPONSIVE_CHANGES.md) for header/hamburger and earlier flight drawer notes (superseded by the unified drawer above).

---

## Desktop filter drawer isolation and logo assets (May 2026)

### Stuck drawer fix (`≥768px`)

The unified OBT filter drawer must not render on desktop:

| Element | Classes |
| --- | --- |
| `#obt-filter-drawer-backdrop` | `hidden md:hidden` (removed from layout at `md+`) |
| `#obt-filter-drawer-panel` | `md:hidden` + mobile bottom-sheet positioning only (no `md:right-0` / `md:max-w-md`) |
| Slide animation | `body.obt-filter-drawer-open` + `.obt-filter-drawer-panel { transform: translateY(100%|0) }` under `@media (max-width: 767px)` only |

`openObtFilterDrawer` no-ops above 767px; `resize` and `showPage` call `closeObtFilterDrawer` when viewport is desktop so teleported filter mounts return to sidebar columns.

### Logo contrast (light vs dark backgrounds)

| Class / context | Asset | Use on |
| --- | --- | --- |
| `.logo-on-light` | `tripsource-logo.png` (legacy; unused on mobile header after May 2026 pass) | — |
| Post-login mobile header | `blue.png` | `#main-content-wrapper` hamburger row (`md:hidden`) |
| Login branding (hero + mobile bar) | `blue.png` (deep corporate wordmark) | `#login-screen` hero top-left (`md:`+), login aside sticky bar (mobile) |
| `.logo-on-dark` | `tripsource-logo-removebg-preview.png` (light/white wordmark) | Dark blue sidebar, login hero / photo overlays |

Do not use the remove-bg preview asset on white UI surfaces—it reads as invisible white-on-white.

---

## Galaxy S20 Ultra — Domestic / International booking (`<768px`)

Scoped to **`#page-booking`** (Domestic and International share this flow via `startBooking()`). Desktop (`md:` / `lg:`) markup and behavior are unchanged except restoring the **in-flow filter column** on results (see below).

| Task | Mobile behavior | DOM / classes | Desktop guard |
| --- | --- | --- | --- |
| Hide stepper & applicant | Not shown on phone | `.booking-stepper-card.hidden.md:block`, `#booking-applicant-type-section`, `#booking-applicant-info-wrap` | `md:block` restores full sections |
| Category nav | Horizontal scroll, icon-over-label cards | `#online-mode-tabs` → `flex flex-nowrap overflow-x-auto scrollbar-hide`; `.online-subtab-card` → `w-[4.75rem] flex-col` + `md:flex-row` | `md:grid md:grid-cols-2 lg:grid-cols-4` |
| From / To stack | TO under FROM; round swap control between | `#page-booking` CSS on `#online-flight-searchbar`, `#online-rail-searchbar` (flex column + centered `.online-flight-searchbar-swap`) | Desktop keeps horizontal bar (no `#page-booking` mobile rules at `≥768px`) |
| Filter UI | 44×44 icon opens `#obt-filter-drawer-*` | `#obt-filter-open-{flight,hotel,rail}.md:hidden`; `openObtFilterDrawer` no-ops at `≥768px` | `#online-*-filter-sidebar.max-md:hidden` + `lg:absolute lg:w-[260px]` + parent `lg:pl-[276px]` |
| Showing Flights density | Tighter outer padding/gaps only | Results header `max-md:px-3 max-md:py-2`; `#online-flight-cards-*` `max-md:p-2 max-md:space-y-2` | MMT card HTML / table at `md:+` untouched |

**Filter note:** On mobile, filter mounts teleport into the global drawer on open. On **`md:`+**, mounts stay in the left sidebar; the wide “Filters” bar is not shown (`md:hidden` on icon triggers).

---

## My Trip Library — mobile layout (`<768px`)

Scoped to **`#page-library`**. Desktop keeps the collapsible **FILTER TRIPS** accordion and reservation-style table (`#library-desktop-shell`, `#library-table-wrap`).

| Task | Mobile behavior | DOM / classes | JS hooks |
| --- | --- | --- | --- |
| Tabs + cards | Filter icon + **Upcoming / History / Review** underline tabs; trip cards from table `data-*` rows | `#library-mobile-shell`, `#library-tab-mobile-*`, `#library-mobile-list-*`, `#library-mobile-empty-*` | `switchLibraryTab`, `syncLibraryMobileCards`, `_libraryMobileCardHtml`, `openLibraryTripDetailFromCard` |
| Hide desktop chrome | No inline filter accordion or table on phone | `#library-filter-card.hidden.md:block`, `#library-desktop-shell.hidden.md:block` | — |
| Filter drawer | Full-height slide-in from right; same filter field IDs | `#library-filter-drawer-*`, `#library-filter-mount` (teleported) | `openLibraryFilterDrawer`, `closeLibraryFilterDrawer`, `applyLibraryFilterDrawer`, `clearLibraryTripFilters`, `applyLibrarySearch` |
| Empty state | Per-tab message when no visible rows | `#library-mobile-empty-{upcoming,history}` | `syncLibraryMobileCards` |

**Filter note:** `#library-filter-mount` holds **Traveler Name**, **Order Style**, and **Travel Date Range** inputs. On mobile it moves into `#library-filter-drawer-body`; on `md:+` it stays inside `#library-filter-panel-body`. Filter algorithms are unchanged (`applyLibrarySearch` / `clearLibraryTripFilters`).

---

## Mobile-first baseline

- Default Tailwind utilities target **narrow phones (~412px width)**, including Samsung S20 Ultra–class viewports (412×915, tall 20:9).
- Typography and page padding use smaller defaults (`text-xl`, `p-4`, etc.) and scale up with **`md:`** (768px and up).

## Desktop guard: `md` (768px)

- **Desktop layout is preserved from 768px upward** (`md:` and above): fixed sidebar width, `#main-content-wrapper` `ml-64` / `ml-16` collapse behavior, dashboard **lg:** grid for three-column news layout, and the **desktop header** (single global search, horizontal actions, visible **JOHN WICK** + LCN block).
- **Mobile-only UI** is isolated with:
  - **`hidden md:flex`** / **`hidden md:block`** — show only on desktop.
  - **`md:hidden`** — show only below `md`.
  - **`max-md:`** variants where useful (e.g. bottom padding for the FAB).

## Breakpoint alignment (important for maintenance)

Previously the app used **`max-width: 1023px`** and **`lg:`** for the drawer and “mobile” header menu. That is now aligned with Tailwind’s **`md` (768px)**:

| Layer | Change |
| --- | --- |
| CSS | `@media (max-width: 1023px)` → **`767px`** for sidebar drawer, `#main-content-wrapper` margin, flight/rail bar grid. |
| CSS | `@media (min-width: 1024px)` → **`768px`** for hiding `#mobile-nav-toggle` and backdrop behavior. |
| JS | All `matchMedia('(max-width: 1023px)')` → **`(max-width: 767px)`** in `setSidebarCollapsed`, `toggleSidebar`, resize handler, and `showPage` (close drawer). |

Keep these three places in sync if you change the breakpoint again.

## Post-login background

- `#main-content-wrapper` is **`relative`** with two absolutely positioned layers (image + scrim), **`max-md` only**, using the same asset as login (`airplane-miniature-notepad-world-map-laptop-wooden-table.jpg`) with **`bg-cover bg-center bg-no-repeat min-h-screen`**.
- **`md:bg-slate-50`** on the wrapper restores the flat desktop look from **768px+** (no hero image on desktop).

## Header (mobile vs desktop)

- **Below `md`:** Identity row (avatar + “John Wick”), stacked **Ask Us** then **Contact Service Team**, utilities, profile control, **hamburger under the profile** (`#mobile-nav-toggle`), then **full-width search**.
- **`md`+:** Original pattern: one `#global-search`, then Contact / Ask Us / bell / language / divider / name + profile (duplicate profile + lang panels use **`data-profile-dropdown`** and **`data-lang-dropdown`**; toggles loop all matching nodes).

## Floating “Ask Us” (mobile)

- **`#fab-ask-us`**: `fixed` bottom-right, **`md:hidden`**, `min-h-[44px]`, respects **`env(safe-area-inset-bottom)`**.
- **`#pages-container`** uses **`max-md:pb-[calc(5rem+env(safe-area-inset-bottom))]`** so scrollable content clears the FAB.

## Trip search (flight / rail)

- Below **768px**, `#online-flight-searchbar` and `#online-rail-searchbar` use **CSS grid** so **From | swap | To** stay on one row; date/class segments span full width.
- **Multi-city rows** (`.online-flight-searchbar--multicity-row`) use the same idea instead of stacking Origin/Destination vertically.

## Approvals filters

- On **`max-md`**, filter fields start **collapsed** (`#approvals-filters-body` has **`hidden md:block`**). **`Show filters` / `Hide filters`** toggles **`hidden`** via **`window.toggleApprovalsFiltersPanel()`**.

## Touch targets and New Request modal

- Key actions use **`min-h-[44px]`** on small screens where applicable; modal uses **`w-[90%]`**, **`max-h-[min(90dvh,915px)]`**, and full-width buttons on mobile with **`md:`** restoration for desktop.

## Files touched

- Primary implementation: **`OBT China.html`** (markup, `<style>`, inline script).
