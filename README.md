# Handoff: Uno Jewellery — Bilingual Gold Jewellery Storefront

## Overview
A landing + storefront experience for **Uno Jewellery**, an Egyptian fine-gold brand. It is a
single-page, bilingual (Arabic RTL / English LTR) marketing + catalog site covering a hero,
category strip, filterable product grid, product detail page, brand story, Instagram feed,
footer, and a login screen. Everything runs client-side with in-memory product data.

## About the Design Files
The files in this bundle are **design references created in HTML** — a working prototype showing
the intended look, layout, copy, and interactions. They are **not production code to ship as-is**.

The file `Uno Jewellery.dc.html` is authored as a "Design Component" and depends on a runtime
(`support.js`) that provides a small templating layer (`<x-dc>`, `<sc-if>`, `<sc-for>`,
`{{ }}` holes) and a `DCLogic` base class. **Do not port the `<x-dc>` runtime into production.**
Instead, **recreate these screens in your target environment** (React/Next, Vue, etc.) using its
established patterns and libraries. The HTML/CSS/copy here is your spec; the logic class documents
the required state and behavior.

To preview the prototype locally, serve the folder over any static server (e.g.
`npx serve .`) and open `Uno Jewellery.dc.html` — it must be served over http, not opened as a
`file://` URL, because `support.js` is loaded relatively.

## Fidelity
**High-fidelity.** Final colors, typography, spacing, imagery, copy (both languages), and
interactions are all present. Recreate the UI pixel-accurately using your codebase's component
library, then wire the described state.

## Screens / Views
The app renders one of three top-level views driven by `state.view` (`'shop' | 'product' | 'login'`).
The `'shop'` view is a long scrolling page containing several sections.

### 1. Home / Hero (`data-screen-label="Home"`)
- **Purpose:** Brand entry point + primary CTA into the catalog.
- **Layout:** Full-bleed section, `height:86vh` (min 560px), background `#2B0D12`, full-cover
  background image (`images/cuff.jpg`, `object-position:center 22%`). A directional gradient
  overlay (`.hero-ov`) darkens the text side — right in Arabic/RTL, left in English/LTR.
- **Components:** eyebrow label, large serif headline (`.disp` — Amiri in AR, Playfair Display in
  EN, `line-height` 1.34 AR / 1.1 EN), sub-copy, gold primary button (`.btn-gold`) + outline
  button (`.btn-outline`). CTAs scroll to the `#grid` section.

### 2. Category Strip
- **Purpose:** Quick jump into product categories.
- **Layout:** `max-width:1320px` centered, `padding:94px 26px 46px`. Centered heading block, then
  `.catstrip` grid: 4 columns desktop → 4 (tablet) → 2 (≤560px), `gap:28px`.
- **Components:** category cards (`.catcard`) with image zoom-on-hover (`scale(1.08)`, `.8s`).

### 3. Shop / Catalog (`data-screen-label="Shop"`, `id="grid"`)
- **Purpose:** Browse + filter products.
- **Layout:** `.shell` grid = `264px 1fr` with `40px` gap (sidebar + product grid). Collapses to a
  single column ≤920px, where the sidebar becomes a **bottom-sheet filter drawer** (`.filterbox`,
  slides up via `transform` when `data-filter-open="1"`, with a scrim and drag handle).
- **Sidebar filters:** karat chips (`[data-chip]`), category rows (`[data-row]`), availability,
  and a price range slider (`input.goldrange`, custom gold track/thumb). Active chip =
  background `#AD8032`, bold, gold shadow.
- **Product grid:** `.prodgrid` = 4 cols → 3 (≤1180px) → 2 (≤920px), `gap:36px 28px`.
- **Product card (`.card`):** image in `.card-img` (radius, shadow), hover lifts card
  `translateY(-7px)`, image zooms `scale(1.07)`, and a WhatsApp CTA (`.card-wa`) fades up. Name is
  clamped to 2 lines (`min-height:44px`).

### 4. Product Detail (`data-screen-label="Product Detail"`)
- **Purpose:** Single product view.
- **Layout:** `max-width:1320px`, `padding:34px 26px 100px`. Breadcrumb nav, then `.pdpgrid`
  two-column (gallery | info), stacks to one column ≤920px.
- **Components:** thumbnail gallery (active thumb tracked by `state.activeImage`), title/price,
  wishlist heart toggle (`state.wishlisted`, fill `#8B4513`), "notify me" (out-of-stock →
  `state.notifyRequested`), expandable Shipping / Care accordions (`state.infoOpen`, animated
  `max-height` + chevron rotation), and a `.relgrid` related-products strip (2 cols on mobile).

### 5. Brand Story / About (`data-screen-label="About"`, `id="story"`)
- **Layout:** Section background `#3A1118`; `.storygrid` two equal columns (image | text), stacks
  ≤920px. Image min-height 440px with zoom.

### 6. Social Proof / Instagram feed
- **Layout:** `.feed-shell` (`#FFFCF5`), `.feedgrid` = 6 cols → 3 (≤920px), `gap:12px`. Square
  tiles (`aspect-ratio:1`) with a hover overlay (`.sqfeed-ov`).

### 7. Footer / Contact (`data-screen-label="Contact"`, `id="footer"`)
- **Layout:** Background `#2B0D12`, text `#D9C4BC`. `.footgrid` = `1.4fr 1fr 1fr 1fr` → `1fr 1fr`
  ≤920px. Brand column, link columns, social buttons (`.footsoc`).

### 8. Login (`data-screen-label="Login"`, `state.view==='login'`)
- **Layout:** Full-height, `.logingrid` two columns (image panel | form), stacks ≤920px.
- **Components:** email + password fields, password show/hide toggle (`state.showPassword` →
  `pwType`), submit returns to shop view. Back button returns to shop.

## Interactions & Behavior
- **View switching:** `goHome`, `backToShop`, `goLogin`, `loginSubmit`, `goGrid/goStory/goFooter`
  (smooth-scroll to sections), product card click → `view:'product'` + set `selectedId`.
- **Language toggle:** `state.lang` `'ar' | 'en'`. Sets `dir`/font family on `.uno` root; `.ar`/
  `.en` spans show/hide; serif, hero line-height, gradient direction, and icon mirroring (`.flip`)
  all switch. **Every string exists in both languages inline** — preserve both.
- **Filtering:** live filter of the product array by karat, category, availability, and max price.
- **Mobile filter drawer:** `state.filterOpen` toggles `data-filter-open` on root; slide-up sheet
  + scrim + apply button.
- **Micro-interactions:** card hover lift/zoom, nav underline grow (`.navlink::after`), button
  hover/active transforms, accordion expand, wishlist/notify toggles, loading skeleton preview
  (`state.previewLoading`, 8 skeleton items).
- **Transitions:** mostly `cubic-bezier(.2,.7,.2,1)` at `.3s–.9s`. Match these easings/durations.

## State Management
Top-level state (from the logic class):
`view`, `lang`, `barOpen` (promo bar), `searchOpen`, `filterOpen`, `karat`, `category`, `maxPrice`,
`availability`, `selectedId`, `activeImage`, `wishlisted`, `notifyRequested`, `infoOpen`
(`'shipping' | 'care' | null`), `showPassword`, `previewLoading`.
Products are a static in-memory array on the component (`this.allProducts`) with per-product
gallery/specs helpers (`galleryFor`, `specsFor`). In production, replace with your data source.

## Design Tokens

**Colors**
- Page background: `#FBF3E9` (warm cream); alt sections `#F5EDDC`, `#FFFCF5`, `#FFFCF5`
- Deep maroon (hero/footer): `#2B0D12`; story panel `#3A1118`
- Primary text: `#2A2320`; muted `#8a8072`
- Gold primary (buttons/active): `#AD8032`; gold accent/eyebrow/underline: `#C1912F`
- Brown accent (outline btn, wishlist): `#8B4513`
- Footer text: `#D9C4BC` / `#F5EAD6`; borders `#ECDFC7`, `#ECE1CE`, `#E4DACB`

**Typography**
- Arabic UI: `Cairo` (300–800). English UI: `Inter` (300–600).
- Display serif: `Amiri` 700 (AR) / `Playfair Display` (EN, `letter-spacing:-0.015em`).
- Eyebrow labels: 12px, weight 600, `letter-spacing:.26em`, gold `#C1912F`.

**Layout**
- Content max-width: `1320px`, side padding `26px`.
- Breakpoints: `1180px`, `920px` (major desktop→mobile shift), `560px`.
- Focus ring: `2px solid #C1912F`, offset 2px.

**Radius / shadow:** card image shadow `0 6px 18px rgba(139,69,19,.10)` → hover
`0 24px 44px rgba(139,69,19,.22)`; gold button shadow `0 10px 26px rgba(120,80,20,.28)`.

## Assets
All in `images/`. Product/lifestyle photography and logo:
- `uno-logo-gold.png` — brand logo
- `cuff.jpg`, `ring.jpg`, `snake.jpg`, `workshop.png` — hero / lifestyle / story shots
- `nk-*.png` (7) — necklaces; `rg-*.png` (13) — rings; used as product + gallery imagery
These are placeholders/prototype imagery — swap for final production assets and a proper CDN.
Icons in the prototype are inline SVG; replace with your icon system.

## Files
- `Uno Jewellery.dc.html` — the full design (template + logic class + inline styles). Everything
  is in this one file: `<helmet>` holds fonts/keyframes/media queries, the template holds markup,
  and the `<script type="text/x-dc">` block at the bottom holds the `Component` logic class.
- `support.js` — the prototype runtime (reference only; do not ship).
- `images/` — all imagery.

## Notes for implementation
- Treat the two-language inline `.ar`/`.en` spans as the source of truth for copy; wire them to
  your i18n system rather than duplicating DOM.
- The `{{ … }}` tokens are template holes bound to values returned by `renderVals()` — read that
  method to see exactly which value feeds each spot.
- RTL is first-class here (default `dir="rtl"`). Make sure your framework/layout handles logical
  properties (`inset-inline-*`, mirrored gradients/icons) the same way.
