# Product Modal Redesign — Full-Page Catalog Overlay

**Date:** 2026-04-08
**Status:** Approved
**Scope:** Replace the current small product modal with a full-page overlay featuring sidebar filters and SKU-level product data.

---

## 1. Problem

The current product modal is a narrow centered dialog (max-width 800px) that shows generic product categories (e.g., "Cotton Seeds (BT Hybrid)") without actual SKU names or filtering. Farmers and dealers need to see specific variety names (e.g., RCH 668 BG II) to make purchasing decisions.

## 2. Solution

A near-full-screen overlay with a two-panel layout:
- **Left sidebar** — vertical filter pills (crop-based for Seeds, type-based for Fertilizers/Pesticides)
- **Right content area** — brand cards in a responsive grid, each listing specific SKUs
- **Pinned bottom bar** — WhatsApp CTA with pre-filled category message

## 3. Layout

### 3.1 Overlay Container
- Size: `95vw × 92vh`, centered with `border-radius: 20px`
- Backdrop: `rgba(0,0,0,0.85)` with `backdrop-filter: blur(20px)`
- Entry animation: slide up from bottom (0.4s)
- Exit animation: slide down + backdrop fade (0.3s)

### 3.2 Overlay Interior Theme
- Background: `var(--cream)` — light earthy theme to contrast the dark backdrop
- Header and sidebar get a subtle `var(--primary-dark)` background strip for contrast
- Content area: `var(--cream)` background with white brand cards

### 3.3 Header
- Background: `var(--primary-dark)` with white text
- Category title (e.g., "Seeds") in `Cormorant Garamond`, 2rem, white
- Subtitle in `rgba(255,255,255,0.7)`
- Close button (×) top-right, 44px circular, `rgba(255,255,255,0.1)` bg

### 3.3 Left Sidebar (~200px)
- Vertical list of filter pills
- Each pill: icon + label, full-width, padding 12px 16px
- "All" filter selected by default (shows all groups)
- Active state: `--primary` background, white text, left 3px accent border
- Inactive: transparent background, `--text-light` color
- Hover: light green background tint

**Filter pills per category:**
- **Seeds:** All, Cotton, Wheat, Jowar, Sunflower
- **Fertilizers:** All, DAP, NPK, Urea, Micronutrients, Nano, Specialty
- **Pesticides:** All, Insecticides, Fungicides, Herbicides, Seed Treatment

### 3.4 Right Content Area
- Scrollable (overflow-y: auto), styled scrollbar
- When "All" selected: shows section headers per group (e.g., "Cotton Seeds", "Wheat Seeds")
- When specific filter selected: shows only that group, no section header needed
- Brand cards in responsive grid: 3-col (desktop), 2-col (tablet), 1-col (mobile)

### 3.5 Bottom CTA Bar
- Pinned to bottom of overlay
- "Interested? WhatsApp for pricing & availability" + green WhatsApp button
- WhatsApp link pre-fills message with current category name

## 4. Brand Card Design

```
┌─────────────────────────────┐
│  (M)  Mahyco                │  ← Monogram circle + brand name
│  ─────────────────────────  │  ← Thin divider
│  • MRC 7373 BG II           │  ← SKU list items
│                             │
│  Cotton · BT Hybrid         │  ← Tag pills at bottom
└─────────────────────────────┘
```

- Background: `var(--white)` on cream section, `rgba(255,255,255,0.06)` on dark section
- Border-radius: `var(--radius)` (12px)
- Brand name: `Cormorant Garamond`, 1.2rem, `--secondary` color (gold)
- Monogram: 40px circle, brand initial, `--primary` bg, white text
- SKU list: `DM Sans`, 0.9rem, bullet prefix, `--text` color
- Tags: small pills (font-size 0.72rem), `--cream-dark` bg, `--text-light` color
- Hover: `translateY(-4px)`, `box-shadow: var(--shadow)`, gold top-border reveal via `::before`

## 5. Mobile Behavior (< 768px)

- Sidebar collapses to **horizontal scrollable pill bar** pinned below header
- Pills become compact chips (icon + short label), horizontally scrollable
- Content area fills remaining height below pill bar
- Cards: single column, full width
- Bottom CTA: stays pinned, slightly smaller padding
- Close button: top-right, z-index above pill bar
- Overlay becomes `100vw × 100vh` (true fullscreen) on mobile

## 6. Animations

| Element | Animation | Duration |
|---------|-----------|----------|
| Overlay entrance | Slide up from bottom + backdrop fade in | 0.4s |
| Overlay exit | Slide down + backdrop fade out | 0.3s |
| Filter switch — outgoing cards | Fade out + slight scale down | 0.2s |
| Filter switch — incoming cards | Fade in with stagger (0.05s per card) | 0.3s |
| Card hover | translateY(-4px) + shadow + gold border | 0.3s |

## 7. Product Data

### 7.1 Seeds (filter by crop)

**Cotton:**
| Brand | SKUs |
|-------|------|
| Rasi Seeds | RCH 668 BG II, RCH 947 BG II, RCH 999 BG II (Amaze) |
| Nuziveedu (NSL) | Mallika Gold (NCS-955 BT-2) |
| Mahyco | MRC 7373 BG II |
| Ankur Seeds | Ankur 3028 BG II |

**Wheat:**
| Brand | SKUs |
|-------|------|
| Certified Varieties | Lok-1, GW 496, HD 2189, HI 1544 (Purna) |

**Jowar (Sorghum):**
| Brand | SKUs |
|-------|------|
| Mahyco | Elegant (MSH-51) |

**Sunflower:**
| Brand | SKUs |
|-------|------|
| Mahyco | MSFH-17, MSFH-17 Plus, Mahyco Black |
| Nuziveedu (NSL) | Sunlight (NSFH-1001), Swathi (NSFH-145) |
| Rasi Seeds | RSH-1 |

### 7.2 Fertilizers (filter by type)

| Type | Brand | Products |
|------|-------|----------|
| DAP | IFFCO | DAP (18-46-0) |
| NPK | IFFCO | NPK 10:26:26, NPK 12:32:16, NPK 20:20:0 |
| Urea | IFFCO | Neem-Coated Urea (46% N) |
| Micronutrients | UPL | Zinc Sulphate, Multi-Micro Mix |
| Nano | IFFCO | Nano Urea, Nano DAP, Nano Zinc |
| Specialty | Syngenta | Water-Soluble Foliar Fertilizers |

### 7.3 Pesticides (filter by type)

| Type | Brand | Products |
|------|-------|----------|
| Insecticides | Syngenta | Ampligo, Alika, Actara |
| Insecticides | UPL | Lancer Gold, Ulala, Phoskill |
| Fungicides | Syngenta | Amistar, Amistar Top |
| Fungicides | UPL | Saaf, Tridium |
| Herbicides | Syngenta | Gramoxone, Touchdown, Rifit Plus |
| Herbicides | UPL | Sweep Power, Sathi |
| Seed Treatment | Syngenta | Cruiser, Maxim |

## 8. Implementation Notes

- All changes are within `index.html` (single-file site)
- Replace the existing `productData` JS object with the new structured data
- Replace the `product-modal` HTML and CSS with the new overlay design
- Replace `openProductModal()` and `closeProductModal()` JS functions
- Add filter state management (active filter, content switching)
- Keep the existing product card click handlers (`data-category` attributes)
- Maintain accessibility: `role="dialog"`, `aria-label`, focus trap, Escape to close

## 9. Files Modified

- `index.html` — CSS (modal styles), HTML (modal markup), JS (data + modal logic)

## 10. Out of Scope

- Backend/database — all data stays hardcoded in JS
- Product images — using monogram icons only
- Pricing — not shown on website (WhatsApp inquiry only)
- Search within modal — not needed for current product count
