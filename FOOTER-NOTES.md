# FoPL Footer — Build Notes

Completed: Day 3, Week 1 (4 July 2026)
File: `fopl-footer.css` (live in **Appearance → Customize → Additional CSS**)

---

## Structure

Built entirely inside **Footer Middle → Widget 1**, as five sequential Kadence blocks (not spread across separate Footer Builder widget slots, which only supports 3 columns natively and can't fit the 4-column brand grid). Structure, top to bottom:

| Widget ID   | Content                                                                             |
| ----------- | ----------------------------------------------------------------------------------- |
| `#block-10` | Main 4-column grid: brand/logo/tagline/social, Quick Links, Contact, Stay Connected |
| `#block-12` | "Through the lens" label                                                            |
| `#block-14` | Lens strip, 4 photo thumbnails                                                      |
| `#block-17` | Utility row: copyright + "Supported by the NRM Community Stewardship Program"       |
| `#block-18` | Acknowledgment of Country banner                                                    |

**Why this order matters:** Kadence's Footer Builder always renders the entire Middle Row before the entire Bottom Row, with no way to interleave them. The correct prototype order (Main → Lens → Utility → Ack) required moving _all_ of this content into the Middle Row's single widget area rather than splitting utility/ack across the separate Bottom Row, since anything left in Bottom Row will always render last regardless of intent.

**These `#block-X` IDs are stable** (sequential, assigned once when each widget block was created), unlike Kadence's auto-generated internal classes (`.kb-row-layout-id...`, `.kadence-column...`), which regenerate on every rebuild. All footer CSS is anchored to these IDs specifically for that reason.

---

## Key confirmed values (from the actual prototype `site.css`, not estimated)

- Mega/lens panel width cap: `560px` (desktop only, removed at `≤900px`)
- Lens strip gap: `12px`
- Social icons: `36px` circles, `12px` gap between them
- Quick Links row gap: `0px` (final adjusted value — this is deliberately tighter than the prototype's own `10px`, per direct instruction)
- Contact email-to-address gap: `20px` (final adjusted value — prototype uses a double `<br>`, this project uses an explicit `margin-bottom` instead for finer control)
- Ack banner: `0px` top/bottom padding, `20px` left/right, `12px` font, `1.3` line-height (final adjusted value, deliberately tighter than earlier drafts)

**Note:** several of the above are Ebun's own fine-tuned adjustments made directly in the live Additional CSS field, not the prototype's literal values. If revisiting this later, don't reset these back to earlier draft numbers, check this file first.

---

## Known quirks worth remembering

- **Acknowledgment bar full-bleed:** achieved with the `width: 100vw` + `margin-left/right: calc(50% - 50vw)` technique, since `#block-18` otherwise sits inside Kadence's `.site-container` max-width wrapper like everything else. This is the standard "break out of a contained layout" trick, worth reusing anywhere else a full-bleed section is needed later in the build.
- **Default WordPress widget spacing:** every `.widget` gets its own default `margin-bottom` from Kadence/WP. With five separate widgets stacked in this footer, that compounds into large, uneven gaps if not explicitly zeroed, this was the real cause of the "too much space below Lens strip" issue, not anything wrong with the row layouts themselves.
- **Quick Links hover:** Kadence's border-based hover simulation system was unreliable (values got "stuck" on individual links, like `kb-nav-link-3roavwf6d` / "What's on", and couldn't be cleared through the Customizer UI). Final approach: killed the border system entirely via CSS custom properties, replaced with plain `text-decoration: underline` on hover.
- **Contact link font mismatch:** Kadence's Advanced Heading block styles body text and any links inside it as two separate internal settings. The paragraph was set to Lato correctly, but the link inside it wasn't, and fell back to a theme default. Fixed by explicitly setting `font-family` on the anchor itself.
- **Gravity Forms styling:** scoped entirely to `#gform_wrapper_1` so it never affects any other form elsewhere on the site (membership forms, volunteer EOI, etc. will need their own scoped treatment when built).
- **Mailchimp connection:** not yet live. The form captures submissions into Gravity Forms regardless (nothing is lost), but the actual Mailchimp feed requires Brett's login or an API key, still pending as of Day 3.

---

## If rebuilding from scratch

1. Re-check this file for the confirmed final spacing values before assuming the prototype's literal CSS numbers are correct, several were deliberately adjusted
2. Rebuild in this exact order inside a single Widget 1 area: Main grid → Lens label → Lens grid → Utility row → Ack banner. Do not split utility/ack into the Bottom Row
3. Re-apply the full-bleed technique to the ack banner immediately, don't wait until a later styling pass
4. Re-confirm the Mailchimp feed is connected before considering the footer fully production-ready
