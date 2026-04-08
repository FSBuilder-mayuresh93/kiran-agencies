# Product Modal Redesign Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the small centered product modal with a full-page catalog overlay featuring sidebar filters and SKU-level brand cards.

**Architecture:** Single-file change to `index.html`. Replace 3 sections: CSS (modal styles), HTML (modal markup), JS (product data + modal logic). No new files, no build step. All changes are self-contained within existing code blocks.

**Tech Stack:** Vanilla HTML/CSS/JS, GSAP (already loaded), CSS variables (already defined)

**Spec:** `docs/superpowers/specs/2026-04-08-product-modal-redesign.md`

---

## File Map

- **Modify:** `index.html`
  - CSS lines ~1426-1631: Replace `PRODUCT MODAL` CSS block
  - CSS lines ~2507-2510: Replace modal responsive overrides in 768px media query
  - HTML lines ~3007-3027: Replace modal HTML markup
  - JS lines ~3782-3861: Replace `productData` object + `openProductModal`/`closeProductModal` functions

No new files. No test files (static HTML site, visual verification via preview).

---

## Chunk 1: CSS — New Catalog Overlay Styles

### Task 1: Replace Product Modal CSS

**Files:**
- Modify: `index.html:1426-1631` (CSS product modal block)

- [ ] **Step 1: Replace the CSS block from `/* PRODUCT MODAL */` through the `@media (max-width: 768px)` modal rules (line 1426 to 1631)**

Replace the old_string starting with:
```css
/* ═══════════════════════════════════════════════
   PRODUCT MODAL
   ═══════════════════════════════════════════════ */
.product-modal {
```
...through to (and including):
```css
    .product-modal-grid { grid-template-columns: 1fr; }
}
```

With new CSS that defines:
- `.catalog-overlay` — fixed fullscreen backdrop with blur
- `.catalog-container` — 95vw×92vh rounded container
- `.catalog-header` — dark green header with title, subtitle, close button
- `.catalog-body` — flex row with sidebar + content
- `.catalog-sidebar` — 200px left panel with filter pills
- `.catalog-filter` — individual filter pill styling + active state
- `.catalog-content` — scrollable right panel
- `.catalog-section-title` — group header (e.g., "Cotton Seeds")
- `.catalog-grid` — responsive card grid (3-col/2-col/1-col)
- `.brand-card` — white card with monogram, brand name, SKU list, tags
- `.brand-monogram` — circular initial icon
- `.brand-card-sku` — individual SKU line
- `.brand-card-tags` — bottom pill tags
- `.catalog-footer` — pinned WhatsApp CTA bar
- Entry/exit animations (slide up/down)
- Filter transition animations (fade crossfade)

- [ ] **Step 2: Replace modal overrides in 768px media query**

In the `@media (max-width: 768px)` block (~line 2507-2510), replace the 4 `.product-modal-*` lines with:
- `.catalog-container` becomes `100vw × 100vh`, no border-radius
- `.catalog-sidebar` becomes horizontal scrollable pill bar below header
- `.catalog-grid` becomes single column
- `.catalog-filter` becomes compact horizontal chips

Also add matching rules in `@media (max-width: 480px)` for smaller screens.

- [ ] **Step 3: Verify CSS parses correctly**

Run preview server, open page, check browser console for CSS parse errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: replace product modal CSS with catalog overlay styles"
```

---

## Chunk 2: HTML — New Catalog Overlay Markup

### Task 2: Replace Product Modal HTML

**Files:**
- Modify: `index.html:3007-3027` (modal HTML block)

- [ ] **Step 1: Replace the HTML block**

Replace old markup from:
```html
<div class="product-modal" id="productModal" role="dialog" aria-label="Product details">
```
...through to the closing:
```html
</div>
```
(the one closing `product-modal`, before the `<!-- WHY CHOOSE US -->` comment)

With new HTML structure:
```html
<div class="catalog-overlay" id="catalogOverlay" role="dialog" aria-label="Product catalog">
    <div class="catalog-container">
        <div class="catalog-header">
            <div>
                <h2 id="catalogTitle">Seeds</h2>
                <p id="catalogSubtitle">Premium hybrid & certified seeds</p>
            </div>
            <button class="catalog-close" id="catalogClose" aria-label="Close">&times;</button>
        </div>
        <div class="catalog-body">
            <div class="catalog-sidebar" id="catalogSidebar">
                <!-- Filter pills injected by JS -->
            </div>
            <div class="catalog-content" id="catalogContent">
                <!-- Brand cards injected by JS -->
            </div>
        </div>
        <div class="catalog-footer">
            <p>Interested in any product? Get pricing & availability instantly.</p>
            <a id="catalogWhatsApp" href="https://wa.me/919920997333" target="_blank" rel="noopener">
                <!-- WhatsApp SVG icon -->
                WhatsApp for Pricing
            </a>
        </div>
    </div>
</div>
```

- [ ] **Step 2: Verify HTML structure renders**

Preview the page, click a product card, verify the overlay opens (will be unstyled until CSS is also in place — if doing tasks sequentially, both CSS and HTML should be in by now).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: replace product modal HTML with catalog overlay markup"
```

---

## Chunk 3: JavaScript — New Product Data + Catalog Logic

### Task 3: Replace Product Data Object

**Files:**
- Modify: `index.html:3782-3819` (productData JS object)

- [ ] **Step 1: Replace `productData` with new structured data**

The new data structure groups by category → filter → brand → SKUs:

```javascript
const catalogData = {
    seeds: {
        title: 'Seeds',
        subtitle: 'Premium hybrid & certified seeds from top Indian brands',
        filterKey: 'crop',
        filters: [
            { id: 'all', label: 'All Seeds', icon: '🌾' },
            { id: 'cotton', label: 'Cotton', icon: '☁️' },
            { id: 'wheat', label: 'Wheat', icon: '🌾' },
            { id: 'jowar', label: 'Jowar', icon: '🌿' },
            { id: 'sunflower', label: 'Sunflower', icon: '🌻' }
        ],
        groups: {
            cotton: {
                title: 'Cotton Seeds',
                brands: [
                    { name: 'Rasi Seeds', initial: 'R', skus: ['RCH 668 BG II', 'RCH 947 BG II', 'RCH 999 BG II (Amaze)'], tags: ['Cotton', 'BT Hybrid'] },
                    { name: 'Nuziveedu (NSL)', initial: 'N', skus: ['Mallika Gold (NCS-955 BT-2)'], tags: ['Cotton', 'BT-2'] },
                    { name: 'Mahyco', initial: 'M', skus: ['MRC 7373 BG II'], tags: ['Cotton', 'BT Hybrid'] },
                    { name: 'Ankur Seeds', initial: 'A', skus: ['Ankur 3028 BG II'], tags: ['Cotton', 'BT Hybrid'] }
                ]
            },
            wheat: {
                title: 'Wheat Seeds',
                brands: [
                    { name: 'Certified Varieties', initial: 'W', skus: ['Lok-1', 'GW 496', 'HD 2189', 'HI 1544 (Purna)'], tags: ['Wheat', 'Rabi'] }
                ]
            },
            jowar: {
                title: 'Jowar (Sorghum) Seeds',
                brands: [
                    { name: 'Mahyco', initial: 'M', skus: ['Elegant (MSH-51)'], tags: ['Jowar', 'Hybrid'] }
                ]
            },
            sunflower: {
                title: 'Sunflower Seeds',
                brands: [
                    { name: 'Mahyco', initial: 'M', skus: ['MSFH-17', 'MSFH-17 Plus', 'Mahyco Black'], tags: ['Sunflower', 'Hybrid'] },
                    { name: 'Nuziveedu (NSL)', initial: 'N', skus: ['Sunlight (NSFH-1001)', 'Swathi (NSFH-145)'], tags: ['Sunflower', 'Hybrid'] },
                    { name: 'Rasi Seeds', initial: 'R', skus: ['RSH-1'], tags: ['Sunflower', 'Hybrid'] }
                ]
            }
        }
    },
    fertilizers: {
        title: 'Fertilizers',
        subtitle: 'Complete crop nutrition from India\'s leading brands',
        filterKey: 'type',
        filters: [
            { id: 'all', label: 'All', icon: '🧪' },
            { id: 'dap', label: 'DAP', icon: '💊' },
            { id: 'npk', label: 'NPK', icon: '⚗️' },
            { id: 'urea', label: 'Urea', icon: '🧂' },
            { id: 'micro', label: 'Micronutrients', icon: '🔬' },
            { id: 'nano', label: 'Nano', icon: '✨' },
            { id: 'specialty', label: 'Specialty', icon: '🌿' }
        ],
        groups: {
            dap: { title: 'DAP', brands: [{ name: 'IFFCO', initial: 'I', skus: ['DAP (18-46-0)'], tags: ['Phosphorus', 'Sowing'] }] },
            npk: { title: 'NPK Complex', brands: [{ name: 'IFFCO', initial: 'I', skus: ['NPK 10:26:26', 'NPK 12:32:16', 'NPK 20:20:0'], tags: ['Balanced', 'Multi-stage'] }] },
            urea: { title: 'Urea', brands: [{ name: 'IFFCO', initial: 'I', skus: ['Neem-Coated Urea (46% N)'], tags: ['Nitrogen', 'Top-dressing'] }] },
            micro: { title: 'Micronutrients', brands: [{ name: 'UPL', initial: 'U', skus: ['Zinc Sulphate', 'Multi-Micro Mix'], tags: ['Deficiency', 'Soil Health'] }] },
            nano: { title: 'Nano Fertilizers', brands: [{ name: 'IFFCO', initial: 'I', skus: ['Nano Urea', 'Nano DAP', 'Nano Zinc'], tags: ['Nano', 'High Efficiency'] }] },
            specialty: { title: 'Specialty', brands: [{ name: 'Syngenta', initial: 'S', skus: ['Water-Soluble Foliar Fertilizers'], tags: ['Foliar', 'Targeted'] }] }
        }
    },
    pesticides: {
        title: 'Pesticides & Crop Protection',
        subtitle: 'Effective pest & disease management from trusted brands',
        filterKey: 'type',
        filters: [
            { id: 'all', label: 'All', icon: '🛡️' },
            { id: 'insecticides', label: 'Insecticides', icon: '🐛' },
            { id: 'fungicides', label: 'Fungicides', icon: '🍄' },
            { id: 'herbicides', label: 'Herbicides', icon: '🌿' },
            { id: 'seedtreat', label: 'Seed Treatment', icon: '💉' }
        ],
        groups: {
            insecticides: {
                title: 'Insecticides',
                brands: [
                    { name: 'Syngenta', initial: 'S', skus: ['Ampligo', 'Alika', 'Actara'], tags: ['Broad Spectrum'] },
                    { name: 'UPL', initial: 'U', skus: ['Lancer Gold', 'Ulala', 'Phoskill'], tags: ['Systemic'] }
                ]
            },
            fungicides: {
                title: 'Fungicides',
                brands: [
                    { name: 'Syngenta', initial: 'S', skus: ['Amistar', 'Amistar Top'], tags: ['Preventive', 'Curative'] },
                    { name: 'UPL', initial: 'U', skus: ['Saaf', 'Tridium'], tags: ['Contact', 'Systemic'] }
                ]
            },
            herbicides: {
                title: 'Herbicides',
                brands: [
                    { name: 'Syngenta', initial: 'S', skus: ['Gramoxone', 'Touchdown', 'Rifit Plus'], tags: ['Pre-emergence', 'Post-emergence'] },
                    { name: 'UPL', initial: 'U', skus: ['Sweep Power', 'Sathi'], tags: ['Weed Control'] }
                ]
            },
            seedtreat: {
                title: 'Seed Treatment',
                brands: [
                    { name: 'Syngenta', initial: 'S', skus: ['Cruiser', 'Maxim'], tags: ['Protection', 'Early Stage'] }
                ]
            }
        }
    }
};
```

- [ ] **Step 2: Commit data change**

```bash
git add index.html
git commit -m "feat: replace productData with structured catalogData (SKU-level)"
```

### Task 4: Replace Modal Logic Functions

**Files:**
- Modify: `index.html:3821-3861` (modal JS functions)

- [ ] **Step 1: Replace modal element references and functions**

Replace old references (`productModal`, `modalGrid`, `modalTitle`, etc.) and functions (`openProductModal`, `closeProductModal`) with:

```javascript
// New catalog overlay references
const catalogOverlay = document.getElementById('catalogOverlay');
const catalogTitle = document.getElementById('catalogTitle');
const catalogSubtitle = document.getElementById('catalogSubtitle');
const catalogSidebar = document.getElementById('catalogSidebar');
const catalogContent = document.getElementById('catalogContent');
const catalogClose = document.getElementById('catalogClose');
const catalogWhatsApp = document.getElementById('catalogWhatsApp');

let currentCategory = null;
let currentFilter = 'all';

function openCatalog(category) { ... }  // Builds sidebar + content
function closeCatalog() { ... }         // Slides out, cleans up
function setFilter(filterId) { ... }    // Switches active filter, re-renders content
function renderContent(category, filterId) { ... } // Generates brand card HTML
function renderBrandCard(brand) { ... } // Single brand card HTML template
```

Key behaviors:
- `openCatalog()`: sets title/subtitle, builds filter pills in sidebar, renders "all" view, opens overlay
- `setFilter()`: updates active pill highlight, calls `renderContent()` with fade animation
- `renderContent()`: if "all" → loops all groups with section headers; if specific → shows just that group
- `renderBrandCard()`: returns HTML string for one brand card (monogram + name + SKU list + tags)
- WhatsApp link updates with category name pre-filled in message
- Escape key closes, backdrop click closes, focus trap

- [ ] **Step 2: Update product card click handlers**

Replace:
```javascript
card.addEventListener('click', () => openProductModal(card.dataset.category));
```
With:
```javascript
card.addEventListener('click', () => openCatalog(card.dataset.category));
```

Also update the Escape key handler and backdrop click handler to reference `catalogOverlay` instead of `productModal`.

- [ ] **Step 3: Verify in preview**

Open the page, click each product card (Seeds, Fertilizers, Pesticides), verify:
- Overlay opens with correct title/subtitle
- Sidebar shows correct filters for category
- "All" view shows all groups with section headers
- Clicking a filter shows only that group
- Cards display brand name, monogram, SKU list, tags
- WhatsApp button works
- Close button, Escape, backdrop click all close
- Mobile: sidebar becomes horizontal pill bar

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add catalog overlay logic with filters and brand cards"
```

---

## Chunk 4: Final Polish & Deploy

### Task 5: Visual QA + Push

- [ ] **Step 1: Test all 3 categories in preview**

For each (Seeds, Fertilizers, Pesticides):
1. Click the product card
2. Verify overlay opens
3. Click each filter — verify correct content shows
4. Verify "All" shows all groups
5. Verify WhatsApp pre-fills correct category

- [ ] **Step 2: Test mobile view**

Resize to 375px width:
1. Verify sidebar becomes horizontal pill bar
2. Verify cards stack single column
3. Verify scroll works in content area
4. Verify close button accessible

- [ ] **Step 3: Final commit and push**

```bash
git add index.html
git push
```

This triggers Vercel auto-deploy. Verify at https://kiran-agencies.vercel.app/
