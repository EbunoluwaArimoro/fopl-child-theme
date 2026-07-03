# FoPL Header — Build Notes

Completed: Day 3, Week 1 (3 July 2026)
Files: `fopl-header-desktop.css`, `fopl-header-mobile.css` (both live in **Appearance → Customize → Additional CSS**, two separate fields)

This header went through a lot of iteration. A handful of things aren't obvious just from reading the CSS, documented here so a future rebuild (by me or anyone else) doesn't have to rediscover them from scratch.

---

## Required custom CSS classes

Several mobile/off-canvas rules depend on **manually-added custom CSS classes** rather than auto-generated block IDs, because Kadence regenerates a new random ID suffix on every block rebuild, which silently breaks any CSS written against the old ID. Class names are stable and survive rebuilds; IDs are not.

If the header is ever rebuilt, these classes need to be re-added via **Advanced tab → Additional CSS Class(es)** on the block noted:

| Class | Applied to | Purpose |
|---|---|---|
| `mp-indent` | The 4 sub-sub nav items (PlantFest26, PlantFest25, Annual reports & docs, Governance docs) — **desktop and mobile navs separately**, this does not carry over between the two | Indented sub-item styling |
| `mobile-cta-row` | Off-canvas Row Layout wrapping the Log in / Join buttons | Stacked, full-width button layout |
| `btn-login-mobile` | Off-canvas "Log in" button | Light-fill styling (not the desktop ghost/outline style, which doesn't read well on white) |
| `btn-join-mobile` | Off-canvas "Join" button | Gold fill, matches desktop |
| `mobile-logo-group` | Row Layout wrapping the mobile logo + title/tagline | Tight, left-aligned logo/text layout |
| `mobile-title-stack` | The text column specifically (holding "Friends of Perry Lakes" + "Floreat · WA"), not the logo column | Title/tagline font, spacing, left alignment |

**Desktop nav** relies on auto-generated IDs directly (e.g. `.kb-btn66_b5ab8d-79`, `.kt-adv-heading66_c97ed5-bb`) since it hasn't needed rebuilding since it stabilized. If desktop is ever rebuilt from scratch, those selectors will need updating to match new IDs, or ideally, add stable custom classes there too at that point.

---

## Responsive breakpoints

- **Desktop header** (mega menus, full nav bar): `≥1100px`
- **Tablet/mobile header** (off-canvas): `<1100px`
- Set in **Appearance → Customize → Header → "Screen size to switch to mobile header"** = `1100`
- Within the mobile/tablet header, a further internal split exists for logo/title positioning:
  - **Phone**: default values (no media query needed)
  - **Tablet**: `768px–1099px`, needs its own offset values (see below), since the same block behaves differently at that width

The prototype itself has **no defined mobile navigation spec** — it only hides the desktop nav below 1100px with a placeholder `.nav__burger{display:none}`. Everything mobile-specific in this build (off-canvas structure, flat-list nav pattern, button treatment) is original work built to match the brand system, not a 1:1 prototype match, since no such reference exists.

---

## Logo/title positioning — exact confirmed values

These were found by testing directly in browser DevTools, not calculated, they compensate for Kadence's own internal column padding/spacing, which isn't fully controllable through standard block settings.

**Phone width** (`.kadence-column66_f5159c-0a.kb-section-sm-dir-vertical`):
```css
margin-left: -25px;
margin-top: -20px;
```

**Tablet width** (`768px–1099px`, same selector):
```css
margin-left: -55px;
margin-top: -5px;
```

**Logo column** (`.kadence-column66_d561ee-fb`):
```css
margin-left: -15px;
```

⚠️ These selectors are ID-based and **will break if this block is rebuilt**. If the logo/title area looks wrong after a future edit, re-test in DevTools at both phone and tablet widths and update these three rules with new values/selectors.

---

## Known quirks worth remembering

- **Kadence's native chevron SVG is hidden entirely** on desktop nav items; a custom CSS-drawn chevron (`::after` pseudo-element, rotated border trick) replaces it. This was necessary because the native SVG couldn't be reliably repositioned across browser/cache states.
- **Off-canvas nav is a separate saved Navigation** from the desktop one (not shared), built as a flat list with Sub Menus rather than Mega Menus, since Kadence can't cleanly collapse mega menu content into a mobile accordion.
- **`!important` is used throughout** — necessary given Kadence's own generated CSS is highly specific. Not ideal practice in isolation, but required here to reliably override block-level styles from the Additional CSS field.
- **Mega panel width**: `300px` exactly, confirmed against the prototype's actual `site.css` (not guessed from screenshots).
- **Descriptions are shown on both desktop and mobile.** Desktop mega menus always showed title+description pairs; mobile was initially set to hide descriptions for a cleaner flat list, but the final direction was to show them there too.

---

## If rebuilding from scratch

1. Re-check this file for every custom class listed above and re-apply them as each block is built
2. Re-test logo/title offsets in DevTools at both phone and tablet widths before finalizing
3. Confirm the `1100px` breakpoint setting in Customizer → Header didn't reset
4. Re-verify `mp-indent` is applied separately in both desktop and mobile navs