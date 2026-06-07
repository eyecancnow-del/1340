# 1340 Cafe & Cocktail Bar — Complete Project Documentation

## Overview
- **Site**: Single-page static HTML/CSS/JS site for 1340 Cafe & Cocktail Bar in Savar, Dhaka
- **File**: `C:\Users\VIBER\Desktop\ALL FUTURE PROJECTS\1340 Simple\index.html` (~2089 lines)
- **Structure**: All HTML, CSS, and JS in ONE file — no dependencies, no build tools
- **Hosting**: GitHub Pages at `https://eyecancnow-del.github.io/1340/`
- **GitHub repo**: `https://github.com/eyecancnow-del/1340` (branch: `master`)
- **Repo owner**: `eyecancnow-del`

## How to Edit the Site (Two Ways)

### Method A: Direct File Edit (Permanent, Recommended)
1. Open `index.html` in VS Code or any text editor
2. Make changes (text, prices, images, etc.)
3. Save the file
4. Open terminal in the project folder
5. Run:
   ```
   git add index.html
   git commit -m "description of changes"
   git push
   ```
6. GitHub Pages auto-deploys in ~1-2 minutes

### Method B: Admin Panel (Browser, Local + GitHub Sync)
- Open the live site, press `Ctrl+Shift+E` (or `Cmd+Shift+E` on Mac)
- Enter the admin password
- Edit text by clicking, upload images by clicking card icons
- Click gold **Save** button → saves to your browser AND pushes to GitHub
- Changes visible to everyone after Save

---

## Admin Panel (Browser Editor)

| Feature | Detail |
|---------|--------|
| Key combo | `Ctrl+Shift+E` / `Cmd+Shift+E` |
| Password auth | SHA-256 hashed, stored in source (obfuscated — split into 9 array pieces) |
| Failed attempts | Locks after 5 tries, shows counter |
| Enter edit mode | Combo → enter password → Save button appears |
| Exit edit mode | Click gold **Save** button (bottom-right) |
| What's editable | All headings, paragraphs, prices, tags, images, hours |
| Card add | Click `+` in any section |
| Card delete | Click `✕` on any card |
| Tag add/delete | Click `+` / `✕` inside tag containers (max 5 tags) |
| Hours editor | Click "Edit Operating Hours" button (visible in edit mode) |

---

## GitHub Sync System

### How It Works
- **Token**: Stored in your browser's `localStorage` (not in source code)
- **On Save**: `ghPushState()` captures all section HTML + hours config → commits `data/state.json`
- **On image upload**: `ghUploadImg()` uploads compressed image to `data/i/` folder in repo
- **On page load**: `ghFetchState()` fetches latest `data/state.json` from GitHub → applies to DOM
- **Fallback**: If GitHub fetch fails, loads from browser's IndexedDB instead

### Token Setup
- Create at `github.com/settings/tokens` → **Fine-grained token**
- Name: anything, Expiration: **No expiration**
- Repository access: **Only select repositories** → `eyecancnow-del/1340`
- Permissions: **Contents → Read and write**
- Paste the token when the Save button prompts you
- Token persists in `localStorage` — only need to paste once

### Key Code References
- `GH_OWNER`, `GH_REPO`, `GH_BRANCH` — line ~1855
- `ghPushState()` — line ~1899
- `ghFetchState()` — line ~1918
- `ghUploadImg()` — line ~1889
- `promptGhToken()` — line ~1857

### To Move to Another GitHub Account
1. Fork or clone the repo to new account
2. Update at line ~1855:
   ```js
   var GH_OWNER = 'new-username', GH_REPO = '1340', GH_BRANCH = 'master';
   ```
3. New owner creates their own fine-grained PAT
4. Enable GitHub Pages in repo Settings → Pages → branch `master`, folder `/ (root)`

---

## Image Upload Flow

### New Flow (After GitHub Sync Update)
1. Click card icon in edit mode → pick image
2. Image is compressed via canvas (`compressImg()` at line ~1515)
3. Shows instantly on card (data URL)
4. Background: uploaded to GitHub `data/i/` folder via `ghUploadImg()`
5. Card image URL replaced with GitHub raw URL
6. On Save, `state.json` with GitHub URLs is committed

### Old Images (Pre-GitHub Sync)
- Stored as base64 data URLs in browser's IndexedDB only
- NOT visible on other devices
- **Fix**: Delete old image card → re-upload (goes through GitHub flow)

### IndexedDB
- Database name: `1340-image-data`, Object store: `store`
- Keys: `'data'` (images), `'state'` (section HTML)
- Migration from old `localStorage` happens automatically on first load

---

## Page Sections

| Section | ID | Editable Type | Description |
|---------|----|---------------|-------------|
| Hero | `#hero` | Titles via admin | Logo "1340", tagline "Coffee • Mocktails • Cuisine", location |
| About | `#about` | Text | Welcome paragraph + image |
| Coffee | `#coffee` | Cards (`.coffee-card`) | 6 coffee items with flip animation |
| Menu | `#menu` | Cards (`.menu-card`) | 4 menu category cards, fullscreen viewer |
| Monin Flavors | `#monin` | Cards (`.coffee-card`) | 4 syrup flavors in 2-column subgrid |
| Signature Mocktails | `#signatures` | Cards (`.coffee-card`) | 3 signature drinks |
| Golden Hour | — | Hours modal | Clock with sunset section, auto open/closed |
| Gallery | `#gallery` | Images | Photo grid, lightbox on click |
| Reviews | `#reviews` | Text (JSON array) | Customer testimonials carousel |
| Location | `#location` | Text + hours | Address, map, operating hours display |

---

## Operating Hours Config

At line ~1134:
```js
var OPERATING_HOURS = {
  openHour: 15,    // 24h format (15 = 3 PM)
  closeHour: 22,   // 22 = 10 PM
  closedDays: [4]  // 0=Sun, 1=Mon, ... 4=Thu (closed)
};
```

- Golden hour clock auto-calculates open/closed
- Supports overnight spans (openHour > closeHour)
- Supports 24/7 (openHour === closeHour)
- Edit via hours modal in admin panel or directly in source

---

## Card Templates

At line ~1402:
```js
var newCoffeeHTML = '<button class="coffee-card">...';
var newSigHTML = '<button class="coffee-card">...';      // Signature mocktails
var newMenuHTML = '<div class="menu-card">...';          // Menu categories
var newFlavorHTML = '<button class="coffee-card">...';   // Monin flavors
```

All use `button.coffee-card` structure with `coffee-card-inner` → `coffee-front` + `coffee-back`.

---

## Reviews

At line ~980:
```js
{ stars: 5, text: '...', author: '...', role: 'Google Review' }
```

- Hardcoded array of review objects
- Rendered by `renderReviews()` (with HTML sanitizer `esc()`)
- GSAP carousel auto-rotates, pauses on hover

---

## Security Measures
- **Admin password**: SHA-256 hash stored in source, split across 9 array pieces — defeats casual "View Source" search
- **Password attempts**: Locks after 5 failed attempts, requires reload
- **GitHub token**: Stored in `localStorage` (browser), never in source code
- **Section state sanitization**: `saveSectionState()` strips `<script>`, `<iframe>`, `<object>`, `<embed>`, `<applet>` + removes all `on*` event handler attributes
- **Review rendering**: Uses `esc()` HTML-entity sanitizer to prevent XSS
- **SRI removed**: SRI `integrity` + `crossorigin` on GSAP scripts removed — blocks loading on `file://` protocol

---

## Animations & Effects
- **GSAP**: Hero letter reveal, section fade-ins, card flip, golden hour sun/moon arc
- **Card flip**: CSS 3D transform on `.coffee-card` click
- **Gold shimmer text**: Hero-sub and hero-tagline have animated gold gradient with `background-size: 200%` sweep
- **Gold divider line**: `.hero-sub::after` — subtle gold line between tagline and location
- **Ambient float**: Monin and signature cards have subtle continuous y-axis GSAP animation
- **Golden Hour sun/moon arc**: Sun drops vertically on scroll, moon fades in as sun leaves viewport

---

## Costs

| Item | Cost |
|------|------|
| GitHub Pages hosting | **$0** (free forever) |
| Custom domain (`.cafe`) | **$4.50 first year**, **$30-45/year renewal** |
| Custom domain (`.com`) | Thousands (premium, already taken) |
| GitHub token | **$0** |
| No custom domain | **$0** at `https://eyecancnow-del.github.io/1340/` |

---

## Important Links
- **Live site**: `https://eyecancnow-del.github.io/1340/`
- **GitHub repo**: `https://github.com/eyecancnow-del/1340`
- **GitHub Pages settings**: `https://github.com/eyecancnow-del/1340/settings/pages`
- **Token management**: `https://github.com/settings/tokens`
- **Instagram**: `https://www.instagram.com/1340.coffeebar`

---

## Common Tasks

### "Add a new coffee drink"
1. Open the site, `Ctrl+Shift+E` → enter password
2. Click `+` at the end of the Coffee section
3. Click the new card's text to edit name, price, description, tags
4. Click the icon area to upload an image
5. Click **Save** → done for everyone

### "Change a price"
1. `Ctrl+Shift+E` → password
2. Click the price text → type new price
3. Click **Save**

### "Change operating hours"
1. `Ctrl+Shift+E` → password
2. Click "Edit Operating Hours" button
3. Set open/close time + check closed days
4. Click **Save** in modal, then **Save** button

### "Fix website emergency — wrong info showing"
Options:
- **Quick fix**: `Ctrl+Shift+E` → edit → **Save** (pushes to GitHub)
- **Full reset**: Delete `data/state.json` from GitHub repo → site reverts to original `index.html`

### "Move site to my own GitHub account"
1. Fork/clone the repo
2. Update `GH_OWNER` and `GH_REPO` at line ~1855
3. Get your own GitHub token
4. Enable GitHub Pages in your repo settings

---

## Architecture Decisions

| Decision | Rationale |
|----------|-----------|
| Single HTML file | No build step, no server, easy to deploy, easy to hand over |
| GitHub Pages hosting | Free, auto-deploys on push |
| Admin panel edits local-only initially | Later upgraded to GitHub sync via PAT |
| IndexedDB for images | Unlimited storage vs localStorage (5MB limit) |
| Gold shimmer instead of CSS wrapper | Cleaner HTML, works with GSAP opacity animation |
| No admin bar | Hidden key combo prevents accidental edits |
| Password hash obfuscated | Defeats casual view-source search, not determined extraction |
| Fine-grained PAT | Restricted to single repo, revocable |
| Canvas compression for images | Reduces file size before GitHub upload |

---

## Debugging

### "Save button says 'Failed to publish'"
1. Open browser console (`F12` → Console tab)
2. Check if there's a GitHub API error message
3. Most likely: token expired or permissions changed
4. Generate a new token at `github.com/settings/tokens` → paste when prompted
5. The token needs: fine-grained, Contents: Read and write, on repo `eyecancnow-del/1340`

### "Images not showing on other devices"
- Images uploaded before the GitHub sync update are stored locally only
- Re-upload the image (delete card → re-add with image)

### "Site not updating after push"
- Wait 1-2 minutes for GitHub Pages to redeploy
- Hard refresh browser (`Ctrl+F5`)
- Check GitHub repo → Actions tab → Pages workflow
- Check repo Settings → Pages → ensure branch is `master` and folder is `/ (root)`

### "GSAP warnings in console"
- "target not found" for `.about-image` and empty selectors — pre-existing, harmless
- GSAP scripts loaded from CDN without SRI — intentional (SRI breaks on `file://`)

---

## File Structure
```
C:\Users\VIBER\Desktop\ALL FUTURE PROJECTS\1340 Simple\
├── index.html              ← THE SITE (all code)
├── images\
│   ├── sun.jpg / sun.png   ← Golden hour sun graphic
│   └── moon.jpg / moon.png ← Golden hour moon graphic
├── backup\                 ← Old backups (reference only)
├── data\                   ← Auto-created by GitHub sync
│   ├── state.json          ← Admin panel edits
│   └── i\                  ← Uploaded images
├── .gitignore
└── SESSION-NOTES.md        ← This file
```
