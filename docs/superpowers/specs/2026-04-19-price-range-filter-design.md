# Price Range Filter — Listings Page

**Date:** 2026-04-19  
**Status:** Approved

## Overview

Add a price range filter to the `/listings` page topbar. The filter is hidden behind a button that opens a Semi UI Popover containing a range slider and two number inputs. The backend already supports `minPrice`/`maxPrice` query parameters; this is purely a UI wiring task.

## Topbar Button

- Added to the existing filter row in `ui/src/components/grid/listings/ListingsGrid.jsx`, placed after the existing filter controls and before the sort controls.
- Label: **"Price"** when inactive; **"Price: {min} – {max} €"** when a filter is active.
- Clicking toggles the Popover open/closed.

## Popover Contents

- **Range slider:** `react-range-slider-input` (already installed, version 3.3.5). Renders a dual-handle slider.
- **Two `InputNumber` fields:** "Min €" and "Max €", placed below the slider, kept in sync with the slider handles bidirectionally.
- **"Clear" button:** Resets both `minPrice` and `maxPrice` to null, closing the filter.
- **Bounds:** Derived from `state.listingsData.listings` on first render (before any price filter is applied). Reflects what's available given other active filters (job, provider, etc.).

## State & Data Flow

1. `minPrice` and `maxPrice` are stored as URL search params via the existing `useSearchParamState` hook (`parseNumber` parser), consistent with all other filters in `ListingsGrid`.
2. Both values are passed into the existing `actions.listingsData.getListingsData(filter)` call.
3. The store spreads `filter` into the API query string (`GET /api/listings/table`).
4. The backend `queryListings()` in `lib/services/storage/listingsStorage.js` already applies `WHERE l.price >= @minPrice AND l.price <= @maxPrice` when these params are present.
5. No new API endpoint or backend changes required.

## Files to Change

| File | Change |
|------|--------|
| `ui/src/components/grid/listings/ListingsGrid.jsx` | Add price filter state, Popover button, slider, and inputs to topbar |
| `ui/src/components/grid/listings/ListingsGrid.less` | Style the Popover contents (slider track, input row, clear button) |

## Reference Implementation

- `ui/src/views/listings/Map.jsx` lines 47–161 — working `react-range-slider-input` setup with URL param sync.
- `ui/src/hooks/useSearchParamState.js` — existing hook used by all other filters.

## Out of Scope

- No backend changes.
- No new components/files — changes are contained to `ListingsGrid.jsx` and its LESS file.
- No preset price buckets ("under 1000 €") — plain slider + inputs only.
