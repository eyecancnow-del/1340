# 1340 Simple — Session Notes

## File
`C:\Users\VIBER\Desktop\ALL FUTURE PROJECTS\1340 Simple\index.html`

Single-file HTML (~1393 lines) with inline CSS/JS — cafe/mocktail bar landing page.

## Data Storage
- **Edit mode data** in `localStorage` under key `1340-data`
- Subkeys: `imgs` (coffee card images), `icons` (coffee+sig flip card images, per-grid `coffee-0`/`sig-1`), `menus` (menu card images)
- **Edit mode toggle:** `localStorage` key `1340-edit-mode` or URL param `?edit`

## Admin Mode (Ctrl+Shift+E or `?edit`)
- Persistent edit mode toggle via localStorage or URL param
- When active: all elements in `sel` get `contentEditable=true` (h2-h4, p, .price, .coffee-tags span, .ingredient span, etc.)
- **Card add/delete:** Red `✕` on each `.coffee-card`, `.menu-card`, `.menu-card.flip-card`; dashed `+` after last card in `#coffee .cards-subgrid`, `#signatures .cards-subgrid`, `#menuGrid`
- **Tag add/remove (max 5):** Small red `✕` inside each `.coffee-tags span` / `.ingredient span`; `+` at end of container — event delegation in document click handler
- **Templates:** `newCoffeeHTML` (button.coffee-card), `newSigHTML` (div.menu-card.flip-card), `newMenuHTML` (div.menu-card)
  - New flip cards get `setupNewCard()` — magnify click handler + random LED speed/trail
  - New menu cards have editable `<h3>` + `.icon` for image upload
- **Menu add/delete:** Menu grid (`#menuGrid`) also gets `+`/`✕` — template inserts simple `.menu-card` with icon + h3
- **⤵ Save** button serializes `document.documentElement.outerHTML` to downloadable `1340-website.html`
- Exiting edit mode kills magnified card animation (gsap.killTweensOf + closeMag), removes all admin UI elements and `contentEditable`

## Image Upload Flow
- **Flip cards** (coffee + mocktails): click `.icon` on card **back** → pick image → resized → shows on back (small thumbnail) + syncs to front (full-bleed)
- **Menu cards**: click `.icon` → pick image → resized → fills card with `background-size: contain`
- **No image processing** — raw upload only, no brightness/filters/enhancers
- **Save:** `saveImgs()` saves per-grid (e.g. `coffee-0`, `sig-2`, `menu-0`) so add/delete won't corrupt image mapping
- **Restore:** reads per-grid keys from localStorage on page load

## Google Reviews
- **Google API key:** `AIzaSyBJ80wYQ-0DQTpH1o52V2lxjHIVQgbGEA0` — needs HTTP referrer + Places API restriction
- **Google Place ID:** currently empty (`var PLACE_ID = '';`) — set to actual Place ID for live reviews
- **Fallback:** 5 hardcoded local reviews shown when Google API is unavailable
- **Function:** `fetchGoogleReviews()` at ~line 900, `renderReviews()` at ~line 840, `initCarousel()` at ~line 880

## Sections
| Section | Type | Details |
|---------|------|---------|
| Hero | Parallax video + typewriter | GSAP letter animations |
| About | Text + image | `Zen Old Mincho` title, golden glow drift on word spans |
| Coffee | 6 flip cards | `cards-col > cards-subgrid`, prices use `Bdt` prefix, admin add/delete |
| Menu | 4 image cards | `#menuGrid` single-column, click → fullscreen overlay, admin add/delete |
| Mocktails | 3 flip cards | `cards-col > cards-subgrid`, admin add/delete |
| Reviews | Carousel | Auto-rotates, dots navigation, hover pauses |
| Gallery | Image grid | Static grid of 9 images |
| Location | Map embed | Google Maps iframe |

## CSS Notes
- No `split-layout`, `scene-col`, `split-visual`, or SVG draw animation classes (removed)
- Custom cursor removed — native cursor used
- Stats section removed entirely

## Menu Fullscreen Viewer
- `<div class="menu-fullscreen" id="menuFS"><img src="" alt="Menu"></div>`
- Click menu card with image → opens; click/Escape closes

## GSAP Scroll Triggers
- Fade-in for most sections
- About section: continuous golden glow drift via `text-shadow` + `color` transition on word spans (not Japanese character scramble)

## Known Issues / TODOs
- [ ] Restrict Google API key to HTTP referrers + Places API only
- [ ] Set Google Place ID for live Google Reviews
- [ ] Update Instagram link (currently `#`)
- [x] Add favicon (inline SVG ☕)
- [x] Admin add/delete for coffee + signature + menu cards
- [x] Tag add/remove (max 5) for .coffee-tags and .ingredient
- [x] Per-grid image indexing for add/delete stability
- [x] Menu section add/delete
- [ ] Deploy to web server for Google Reviews to function

## Current State Summary
- **Racing LED comet tail** (50px radial gradient, blur 12px, 0.8-2.0s trail delay) — only active on `.flipped.magnified`
  - Base speed: coffee 3.45s, mocktail 4.6s, randomized 1.15-1.5x per card
- **Card magnify-to-center** (1.5x, GSAP) — click flip card to center+enlarge+flip, overlay/Escape to close
- **Admin mode:** add `+` / delete `✕` for cards + tags, contentEditable on all text, save-as-HTML download
- **openMag/closeMag** exported globally for cross-IIFE access (admin edit mode can force-close magnified card)
- ~1393 lines total
