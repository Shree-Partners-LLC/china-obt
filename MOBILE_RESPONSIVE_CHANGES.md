# Mobile Responsive Changes (Galaxy S20 Ultra — 412×915px)

Target viewport: **412×915px** (Samsung Galaxy S20 Ultra). Breakpoint strategy: **mobile-first** with Tailwind `max-md` (&lt;768px) for phone-only UI and **`md:`+** unchanged for desktop.

---

## Task 1 — Mobile header refactor

### Structural change

Replaced the stacked mobile header (duplicate JW + name, Ask Us, Contact, bell, language, profile, menu) with a single row:

| Zone | Content |
|------|---------|
| Left | `#mobile-nav-toggle` (hamburger) → `window.toggleSidebar()` |
| Right | JW profile chip + `data-profile-dropdown` |

### Tailwind mapping

| Element | Classes | Breakpoint |
|---------|---------|------------|
| Mobile header row | `flex justify-between items-center px-4` | `md:hidden` |
| Hamburger | `min-w-[44px] min-h-[44px]` (touch target) | mobile only |
| Profile button | existing rounded-full JW chip | mobile only |
| Desktop header block | `hidden md:flex md:items-center md:gap-4` | **unchanged** |
| Global search | `md:flex-1 md:max-w-sm` | shared; layout unchanged at `md:` |

### Removed on mobile only

- Top-left JW avatar + “John Wick” label
- Static **Ask Us** text button in the header
- Contact Service Team, bell, and language controls from the mobile header row

### Preserved

- **`#fab-ask-us`** — floating bottom-right Ask Us (`fixed z-[35] … md:hidden`); not moved or hidden
- All desktop (`md:`+) header markup and behavior
- `window.toggleSidebar`, `window.toggleProfileDropdown`, `window.toggleAskUsChat` — no changes to filter/search/session logic

---

## Task 2 — Airbnb-style flight filter drawer (mobile only)

### Structural change

1. **Mobile trigger** — `#mobile-flight-filters-open` (`md:hidden`, sticky above results)
2. **Backdrop** — `#flight-filter-drawer-backdrop` (`fixed inset-0 z-[60] bg-black/50 backdrop-blur-sm`, `md:hidden`)
3. **Same filter DOM** — `#online-flight-filter-sidebar` holds all existing controls (single set of IDs/checkboxes/sliders for `applyFlightFilters` / `clearFlightFilters` / `populateFlightAirlineFilter`)

### Why not `hidden md:block` on a duplicate sidebar

Duplicating filter inputs would break `getElementById` and `querySelectorAll('[data-flight-filter]')`. Instead:

| Viewport | Sidebar behavior |
|----------|------------------|
| **&lt; md** | `max-md:fixed max-md:inset-0 max-md:w-full max-md:z-[61]`; off-screen `translate-y-full`; slides to `translate-y-0` when `body.flight-filter-drawer-open` |
| **≥ md** | Original layout: in-flow `mb-4`, `lg:absolute lg:left-0 lg:top-0 lg:bottom-0 lg:w-[260px]`, parent `lg:pl-[276px]` — **unchanged** |

Equivalent to “hide static sidebar on mobile, show on desktop” without cloning controls.

### Drawer chrome (mobile only — `md:hidden`)

| Area | UI |
|------|-----|
| Header | Title “Filters”, **Clear All** → `clearFlightFilters()`, **X** → `closeFlightFilterDrawer()` |
| Body | Existing Stops, Departure Time, Airlines, Price Range, CO₂, Luggage, Meal blocks |
| Footer | Sticky **Apply Filters** → `applyFlightFiltersAndCloseDrawer()` |

Desktop filter header row uses `hidden md:flex` so the in-sidebar title/Clear All row is unchanged on `md:`+.

### Animation

```css
@media (max-width: 767px) {
  #online-flight-filter-sidebar {
    transform: translateY(100%);
    transition: transform 0.3s ease-in-out;
  }
  body.flight-filter-drawer-open #online-flight-filter-sidebar {
    transform: translateY(0);
  }
}
```

Tailwind on the aside: `max-md:translate-y-full max-md:transition-transform max-md:duration-300`, `md:translate-y-0`.

### New UI-only helpers (no changes to filter algorithms)

- `window.openFlightFilterDrawer()`
- `window.closeFlightFilterDrawer()`
- `window.applyFlightFiltersAndCloseDrawer()` — calls existing `applyFlightFilters()` then closes

Resize handler closes the drawer when crossing to `md:`+.

---

## Breakpoint reference

| Token | Width | Usage |
|-------|-------|--------|
| `max-md` | &lt;768px | S20 Ultra, mobile header, filter drawer, FAB |
| `md:` | ≥768px | Desktop header, visible filter sidebar |
| `lg:` | ≥1024px | Absolute filter column + results offset (unchanged) |

---

## Files touched

- `OBT China.html` — header, flight results filter UI, scoped CSS, drawer open/close helpers
- `MOBILE_RESPONSIVE_CHANGES.md` — this document

## Verification checklist (412×915)

- [ ] Header: menu left, JW profile right; no duplicate JW or header Ask Us
- [ ] FAB Ask Us visible bottom-right over scroll content
- [ ] Flight search → results → **Filters** opens full-height sheet
- [ ] Backdrop tap, X, and Apply close drawer; Clear All resets controls
- [ ] ≥768px: header and left filter sidebar match pre-change desktop layout
 


 # UI Architecture Audit: Restore Core Navigation Modules on Desktop

## Problem Statement
During a mobile responsive layout refactor, structural display modifiers (`hidden`, structural media queries, or conditional viewport states) caused an aggressive layout regression on desktop screens. Key system modules—including **My Trip Library**, **Approvals**, **Reports**, and **User Management**—have been completely omitted from the wider viewports.

## Objective
Audit all application navigation sheets, sidebar configurations, and view-state hooks to safely lift layout suppression on desktop (`screen >= 768px`) while maintaining pristine responsive mobile behaviors.

---

## Cursor AI Targeting Prompt
Mobile responsive breakouts -------------------------------------------------------

### Objective
Review the layout, sidebar, navbar, permissions, or conditional rendering logic across the entire codebase. Locate and restore the missing layout sections on Desktop views, ensuring they use correct mobile-scoping classes so they hide gracefully on mobile but remain fully functional and visible on desktop.

### Missing Content to Restore:
1. "My Trip Library" (Dashboard/Navigation Module)
2. Travel Application -> "Approvals" section
3. Administration -> "Reports" module
4. Administration -> "User Management" module

### Technical Bug Analysis Context:
This regression likely happened because mobile-first utility prefixes or visibility classes were applied incorrectly (e.g., an accidental `hidden md`, `max-md:hidden`, or incorrect logical toggles like `isMobile && <Component />` without a desktop fallback). 

### Exact Execution Steps:
1. Search globally for the terms "My Trip Library", "Approvals", "Reports", and "User Management" to locate their parent nav components, sidebar lists, or routing links.
2. Check the container wrapper classes of these items. If you find responsive classes hiding them (like `hidden`, `md:hidden`, `lg:hidden`), correct them so they display on desktop (e.g., using `md:block`, `md:flex`, etc.).
3. If they are being conditionally rendered using JavaScript window hooks (e.g., `useWindowSize` or `isMobile`), fix the logic expression so desktop screens render them seamlessly.

### [CRITICAL SAFETY GUARDRAILS]:
- Do Not Break Mobile UI: Ensure that making these visible on Desktop does not clutter or break the mobile layout. Use proper responsive prefixes to scope visibility.
- Zero Functional Regressions: Do not modify, delete, or break any click handlers, routing links, API hooks, state variables, or TypeScript types connected to these modules.

Scan the codebase, find the source files handling these navigation layers, and present the clean code diff to restore visibility.