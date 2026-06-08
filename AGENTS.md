# 1340 Cafe & Cocktail Bar — Agent Session State

## Project
Single-file static site (`index.html`) for 1340 Cafe & Cocktail Bar with browser-based admin panel. GitHub Pages at `eyecancnow-del.github.io/1340/`. Repo: `eyecancnow-del/1340` (branch: `master`).

## Current State (Jun 8, 2026)

### Fixed this session
- **IIFE SyntaxError**: Lines 1970-1972 had extra `)` and `}` in compressed `openImgDB().then(...)` chain. Decompressed into named `_doCleanup()` function. Caused ALL JS after it to crash — `restoreSectionState`, `ghFetchState`, `setEdit` never ran. Page showed default HTML (clean slate).

### Pushed to GitHub
- `13f0f69` — `fix: JS syntax error in IIFE causing clean slate`

### Remote state
`data/state.json` is intact (9 keys: coffee, signatures, monin, menu, gallery, _hours, _heroBg, _heroSub, _heroTagline). User should hard refresh (`Ctrl+F5`) to restore edits.

### User is looking for character `➳` (U+27B3)
Confirmed: not present in any file (source HTML, CSS, JS, state.json, images). Zero occurrences in the entire codebase.

## Key Architecture
- **Key combo**: `Ctrl/Cmd+Shift+E` (single keypress, NOT typed sequence)
- **Save button**: Exits edit mode + commits to GitHub via `ghPushState()`
- **Page load**: Cached GitHub state applied synchronously from `1340-gh-cache` localStorage → parallel fetch of GitHub `data/state.json` → IndexedDB fallback
- **Hero fields** saved as separate keys (`_heroBg`, `_heroSub`, `_heroTagline`) to preserve GSAP references
- **`backface-visibility`** needs `-webkit-` prefix for iOS Safari
- **Background**: WebP (237KB) with PNG (2.5MB) CSS fallback

## Next Steps
1. User should hard refresh browser to verify SyntaxError fix restored all edits
2. Investigate where the `➳` character appears if user provides more context

## Important File Locations
- `index.html` — single ~2096 line file (all HTML/CSS/JS)
- `SESSION-NOTES.md` — comprehensive project documentation
- `images/bg.webp` / `images/bg.png` — full-page background
- `data/state.json` — admin panel edits (auto-created on first Save)
