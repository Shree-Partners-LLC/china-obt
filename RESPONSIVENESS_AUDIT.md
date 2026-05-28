# iPhone Responsiveness Audit — Resolution Log

Resolved in `index.html` (May 2026). Desktop (`md:` / ≥768px) unchanged except shared tokens (OTP size applies globally; still within HIG).

## Critical functional blockers — fixed

| Issue | Fix |
|-------|-----|
| International travel forced to domestic on mobile | Removed `startBooking` viewport guard |
| User Management redirected to dashboard on mobile | Removed `showPage` viewport guard |
| Side-nav hid International + User Management | Removed `#nav-international` / `#nav-user-management` `display: none` |

## Layout — fixed

| Issue | Fix |
|-------|-----|
| Itinerary tabstrip clipped when wrapped | `overflow-y: visible` on itinerary tab wrappers only (`max-width: 767px`) |
| Dashboard 3-up crowding on 320px | `grid-cols-1 min-[450px]:grid-cols-3`; removed 7px label CSS |
| Approvals / results tables on phone | Already use mobile cards (`md:hidden`); no table change |

## iOS Safari — fixed

| Issue | Fix |
|-------|-----|
| Input zoom on focus | `font-size: 16px` on inputs in `#login-screen` and `#main-app` (`max-width: 767px`) |
| Safe area on filter/sort footers | `pb-[max(1rem,env(safe-area-inset-bottom))]` on `.obt-filter-drawer-footer`; `pb-[env(safe-area-inset-bottom)]` on `#price-sort-sheet` |
| Viewport safe areas | `viewport-fit=cover` on meta viewport |

## Accessibility — fixed

| Issue | Fix |
|-------|-----|
| OTP cells 40px | 44px (`2.75rem`) + 16px font |
| Flight/hotel action buttons 36px | `min-h-[44px]` (2 instances) |
| Dashboard label tracking | `max-md:tracking-normal` on stat labels |

## Manual test matrix

| Check | iPhone (&lt;768px) | Desktop (≥768px) |
|-------|-------------------|------------------|
| Login / OTP / reset | | |
| Dashboard stats + Start Travel | | |
| Domestic booking + filter drawer | | |
| International booking (not forced domestic) | | |
| Approvals + Library + Reports | | |
| User Management (nav + cards + modal) | | |
| Profile / filter drawers / price sort | | |
