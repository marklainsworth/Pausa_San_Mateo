# Pausa San Mateo menu: changes from Anthony's v1 to v12.11

**Base:** `menus.cellareye.com/pausa-san-mateo/v1.html` (Anthony's version)
**Result:** `pausa-san-mateo-v12.11.html`
**Edited by:** Mark

Everything below is data-driven through `CONFIG` where possible, so other restaurants are unaffected unless they add the same keys. The renderer additions are generic.

---

## 1. New cover page

The menu now opens on a full-screen cover before the list.

- A full-bleed photo of Pausa's dining room, with one light panel over it holding the text.
- The panel is 90% cream with a slight blur of the photo behind it, a gold rule along the top, and a drop shadow.
- **Panel contents:**
  - pause-mark logo
  - "PAUSA / Wine List"
  - intro line
  - a 2×2 grid of how-to tips: *Tap a wine's name*, *Ask the virtual somm*, *Browse by section*, *Look for the fork & knife*
  - **Open the contents** button
  - address and phone
- The fork-and-knife tip uses the same icon the menu shows next to wines that have pairings. The copy in `{wines}` and `{glass}` fills in live counts from the data.
- The photo is darkened only slightly, at the bottom edge behind the panel.

**Config:** `CONFIG.cover`
- `imageUrl`: currently an embedded JPEG (1800px, ~300 KB).
- `imagePosition`: `50% 30%`, which frames the wall hanging.
- `logoUrl`: an embedded 180px copy of the logo. This fixed the logo sometimes not loading on iPhone.
- `lede`, `features[]` (icon, title, text), `cta`.

**Intro copy:**
> At Pausa, our love of Italian food is rivaled only by our love of Italian wine. Tap any bottle for tasting notes, the story of its producer, how it drinks, and what to pair it with.

### How the cover differs by device

| | iPhone (under 768px) | Tablet (portrait) | Desktop |
|---|---|---|---|
| Panel width | 330px max. About 30px of photo shows each side on a 390px phone (20px minimum). | 607px | 440px |
| Panel position | Centered, near the bottom (72px up, to clear the CellarEye badge) | Centered, lifted 48px + 10% of screen height | Centered, 48px from the bottom |
| Logo | 44px | 75px | 54px |
| "PAUSA" title | ~26px | 58px | 42px |
| "Wine List" label | 12px | 14px | 9px |
| Intro paragraph | 15px | 17px | 12px |
| Tip titles | 12.5px, always one line | 16px | 11.5px |
| Tip descriptions | **Hidden** (titles only) | 15px, shown | 11px, shown |
| Tip grid | 2×2, each column sized to its longest title, centered with a 6px gap | 2×2, equal columns | 2×2, equal columns |
| Button text | 13px, 40px tall | 15px, 47px tall | 10.5px |
| Address & phone | 13px | 15px | 11px |

**How "tablet" is detected.** A device gets the tablet cover in either of two cases:
1. The window is 700–1199px wide and in portrait, or
2. A JS check sets `html.is-tablet` for any touch device whose shorter screen side is 600px or more.

The second check catches real iPads a width-only rule misses: iPad mini (744px), 13" iPad Air and Pro (1032px), and iPads with a keyboard or trackpad, which report a mouse. Tested on iPad mini, iPad 10th gen, iPad Pro 11", iPad Pro 13", and iPad Pro 13" with a trackpad.

**Short and narrow screens:**
- Phones narrower than 370px may wrap a tip title, since there isn't room for one line.
- Phones 720px tall or shorter use tighter tip spacing.
- Screens 540px tall or shorter hide the address, so the logo, title and button stay on one screen.

---

## 2. Portrait only on phones and tablets

A web page can't lock screen rotation on iOS. So when a touch device is held sideways, a full-screen slate notice covers the page: *"Please turn your device upright."* Turning it back returns the guest exactly where they were.

- The check uses the device's orientation **and** requires the visible window to be wider than it is tall. That prevents a false trigger when the Android keyboard opens, and on iPads with trackpads.
- **Desktops are never affected**, at any window size.
- On Android, if the menu is saved to the home screen, the page also asks the browser to lock to portrait.
- **Staff iPads:** use Rotation Lock or Guided Access as well.

---

## 3. New contents page

The cover button leads here. The Pausa header shows at the top; the search and filter bar is hidden.

**Section groups** come from `CONFIG.print.runs`, so the digital contents match the printed booklet:
- **Bottiglia:** By the Glass (21), Bolle (14), Bianchi (32), Rosato (4), Rossi (65), Cellar Selections (10)
- **By Region:** Italy map (see section 4)
- **Cocktails & Beverages** and **Spirits**, each section with its count

Rows use dotted leaders with counts where page numbers would be. Tapping a row opens the menu filtered to that section. The page ends with **See the full list** and **Ask the virtual somm**, which opens the full list with the cursor in search.

**Cover button (top-left of header, v12.11).** It's the same control as on the menu pages, lined up with the menu pages' search column. It replaces the "Back to the cover" link that used to be at the bottom.

**Config:** `CONFIG.contents` holds `title` and `intro`, and optionally `groups` to override the print runs.

### How the contents page differs by device

| | iPhone | Tablet & Desktop (768px+) |
|---|---|---|
| Groups after the map | One column | Cocktails & Beverages and Spirits side by side |
| Bottiglia block | Full width | Full width, same width as the map section |
| Map + region list | Map, then the region list in 2 columns | Map on the left, single-column region list on the right |
| Region hover | n/a | Hovering a region, its badge or its list row highlights all three |
| Cover button | 16px from the left edge | Inside the 600px column, matching the menu pages |

---

## 4. Italy region map (on the contents page)

- Shows the 20 regions, shaded from light slate (few wines) to Pausa's dark slate (most wines). Liguria and Molise have no wines, so they're pale and not clickable.
- Gold-ringed badges show each region's count.
- Tapping a region, badge or list row opens the menu filtered to that region. A dark bar shows "Wines of Piemonte" with **Map** and **All wines ✕** buttons.
- **Region lookup:** the backend doesn't send a region, so the page carries a lookup table of about 150 Italian appellations mapped to regions. All 125 Pausa wines resolve. Edge cases checked:
  - Lugana and Riviera del Garda → Lombardia
  - Trentodoc and Teroldego → Trentino-Alto Adige
  - "Venezia Giulia" → Friuli-Venezia Giulia
- If `w.adminRegion` is ever sent, the page uses that first. Wines that don't resolve are simply left off the map.
- **Boundaries:** ISTAT data (CC BY 4.0), simplified to about 17 KB of SVG paths embedded in the page. The license requires attribution, so the credit line under the list must stay.

**Config:** `CONFIG.contents.regionMap` holds `title` and `intro`, plus `after: "Bottiglia"`, which places the map between Bottiglia and Cocktails. The map only appears when `regionMap` is set.

---

## 5. Navigation bar (ported from the Camper template)

The single wrapping chip row (about 8 lines tall on a phone) is replaced with Camper's **two-row, horizontally scrolling** nav, in Pausa colors.

- **Row 1:** Contents · All · Bottiglia · Cocktails & Beverages · Spirits · By the Glass
- **Row 2:** the sections inside whatever is selected in row 1. Tapping a section filters to it; tapping it again returns to the whole group.
- Each row swipes on its own, with edge fades showing more chips.
- Groups come from the new `CONFIG.sectionGroups`, which mirrors the print runs. As on Camper, grouping only turns on above 10 sections.
- Bar height on a phone went from ~300px to ~115px.
- **Deep links** keep working, and the browser Back button works between pages:
  - `#contents`, `#menu`, `#menu/rossi`, `#menu/region-toscana`
  - `#menu/gin` lights Spirits in row 1 and Gin in row 2.

**Only the nav was ported from Camper.** Camper's other engine updates (pairing matching, other modal changes) were not brought over.

---

## 6. Bianchi and Rosato split into separate sections

- The backend still sends one section, "Bianchi & Rosé." The page splits it **on every load** by `wineType`: WHITE and ORANGE → **Bianchi**, ROSE → **Rosato**.
- **Config:** `CONFIG.sectionSplit`. Both new labels were added to `sectionOrder`, `sectionGroups` and `print.runs`. The old "Bianchi & Rosé" label was kept in `print.runs` so the current print template still works.
- Old links such as `#menu/bianchi-rose` and `#menu/bianchi-rose~rose` land on the new sections.
- `slug()` now strips accents, so "Rosé" → `rose` and "Cachaça" → `cachaca`. Before, those became `ros` and `cachaa`.

---

## 7. Tasting card: Wine Profile section (ported from Camper)

- Camper's **Wine Profile** tech sheet (icon rows) is added below the tasting note. Card order now matches Camper: producer story → 1–5 tasting profile bars (unchanged) → tasting note → Wine Profile.
- **Rows showing on Pausa today:**
  - Vintage (NV when missing)
  - Appellation
  - Origin (fallback: the map region, e.g. "Umbria, Italy")
  - Varietal
  - Color and Type, from `wineType`
- **Rows hidden until the backend sends the data:** Producer, Classification, Designation, Vineyard.
- The page accepts either field name (`producerOverview` or `producerSummary`, `type` or `wineType`), so switching Pausa to Camper's export format won't break anything.

### How the tasting card differs by device

| | iPhone | Tablet & Desktop |
|---|---|---|
| Wine Profile | Collapsed behind a **MORE +** button at the bottom of the card. Tapping it slides the section open, scrolls the card (not the page) to show it, and changes the button to **LESS −**. Each card opens collapsed. | Always open, no button |
| Reduced motion | Opens instantly, no slide | n/a |

---

## 8. Staff and guest controls

**Cover button ("close the book").** On menu pages it sits left of the search box; on the contents page, top-left of the header. It resets for the next table: clears search, sets the filter back to All, closes any open card, scrolls the page and both nav rows back to the start, and returns to the cover.

**Back-to-top arrow.** A small, partly transparent round chevron. It appears after scrolling about 1.2 screens down and never shows on the cover. It scrolls smoothly, or jumps if the device has reduced motion on.

| | iPhone | Tablet | Desktop |
|---|---|---|---|
| Arrow position | Bottom-left, 16px in | Bottom-left inside the menu column | Bottom-left inside the 1000px column |

It is placed on the left so it never overlaps the CellarEye badge.

---

## 9. Code notes

- New views (cover, contents) use body classes `page-cover`, `page-contents` and `page-menu`, plus hash routing (`route()`, `go()`, `showPage()`).
- Menu chrome is hidden with `!important` on the cover and contents pages, because `applyView()` writes inline display styles.
- **File size is ~700 KB**, because the cover photo and logo are embedded as data URIs. Host `pausa-cover.jpg` and the logo next to the menu and point `CONFIG.cover.imageUrl` and `CONFIG.cover.logoUrl` at them. That brings the file back to ~350 KB.
- **Pipeline comments.** The data-injection comment names internal tools and the agent workflow (`menu_data.py`, `RestaurantMenuFiles`, "updated by Claude Agents"). Consider stripping comments in the public build and keeping that documentation in a private repo.

---

## 10. Open items for the backend

1. **Pairing data.** `SOURCE_CONTENT.pairings` is empty. The cover's "what to pair it with" line and fork-and-knife tip depend on it, and the pairing icons and Pairings view appear automatically once it's loaded.
2. **Six wines have no tasting profile or tasting note:**
   - Bellavista Rosé Brut
   - Elio Altare Arborina
   - Ermanno Costa San Defendente Roero Riserva
   - Francesco Rinaldi Brunate
   - Nada Fiorenzo Montaribaldi
   - Oddero Villero
3. **Regenerate Pausa's WINES export in Camper's format** with `producer`, `origin`, `classification`, `designation` and `vineyard`, so the rest of the Wine Profile fills in.
4. **Send Bianchi and Rosato as separate sections** from the backend. `sectionSplit` can then be removed.
5. **Print template.** It doesn't know about the Bianchi/Rosato split, so the booklet still shows "Bianchi & Rosé."
6. **Optional:** send an `adminRegion` per wine instead of relying on the page's appellation lookup.
