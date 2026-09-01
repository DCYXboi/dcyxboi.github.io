# DESIGN.md — dcyxboi.github.io

Personal portfolio for **Danny Chan Yi Xiang** — cybersecurity and data-center
engineer, Petaling Jaya, Malaysia. Audience: recruiters and hiring managers for
security operations, application support and infrastructure roles.

- **Live:** https://dcyxboi.github.io
- **Repo:** https://github.com/DCYXboi/dcyxboi.github.io (GitHub Pages, `main` branch, root)
- **Stack:** one hand-written `index.html`. No framework, no build step, no
  dependencies. Vanilla CSS in a single `<style>`, vanilla JS in a single
  `<script>`. The only external host touched is Google Fonts.

---

## 1. Direction

**Dark cinematic and kinetic.** A near-black stage, oversized display type that
reveals itself in motion, one electric signal-lime accent, and a live node field
behind the name. The brief was an award-caliber front-end / UI-UX feel (the
references were awwwards.com and sharplink.com), delivered on top of unchanged,
truthful résumé content. The signal-lime accent and the node field keep a thread
back to the person's actual world (terminals, SOC, networks) without literal
terminal cosplay.

Dark is the default; a toggle in the nav switches to a full light theme and the
choice persists in `localStorage`. System preference is respected until the
visitor chooses.

**Reject on sight:** AI-purple/blue glow, glassmorphism on content, three-equal
feature cards, Inter as a display face, serif display (this rebuild deliberately
retired Instrument Serif), custom mouse cursors, em-dashes anywhere in copy,
rounded corners (the page is square throughout; the only radius is a 4px favicon).

---

## 2. Tokens

Every colour is `oklch`. The bare `:root` carries the **dark** palette in full;
light is redefined under both `prefers-color-scheme: light` and
`:root[data-theme="light"]`. Never define a colour only inside a media query.

### Dark (default)
```
--bg        oklch(0.165 0.006 150)   page ground, warm near-black
--bg-1      oklch(0.205 0.007 150)   raised surface (project band, readout)
--bg-2      oklch(0.245 0.008 150)   hover surface
--fg        oklch(0.965 0.004 150)   primary text            (17.4:1 on --bg)
--fg-soft   oklch(0.795 0.006 150)   secondary text          (10.1:1)
--fg-faint  oklch(0.635 0.006 150)   labels, meta            (5.6:1 — do not darken)
--line      oklch(0.30 0.006 150)    hairlines
--accent    oklch(0.885 0.19 128)    electric signal-lime    (14.2:1)
--accent-2  oklch(0.80 0.16 165)     cooler green, gradients only
--accent-ink oklch(0.19 0.05 150)    text on an accent fill  (13.5:1 on --accent)
--net       196, 236, 120            rgb triplet for canvas
```

### Light
```
--bg        oklch(0.975 0.004 130)
--fg        oklch(0.195 0.008 150)
--fg-faint  oklch(0.455 0.009 150)   (6.75:1 on --bg)
--accent    oklch(0.53 0.16 138)     deeper green for contrast (4.61:1)
--accent-ink oklch(0.99 0.01 150)    near-white on the accent  (4.84:1)
--net       70, 120, 40
```

**One accent only.** Neutrals are all tinted to hue 150 (cool green-grey). `--net`
is an rgb triplet because `canvas` `fillStyle` cannot be trusted to parse `oklch`.

### Measured contrast — treat as floors
Every text/background pair clears WCAG AA (4.5:1) in **both** themes. `--fg-faint`
sits on 11–14px text, so it is bound by the 4.5:1 normal-text floor.

---

## 3. Type

Loaded from Google Fonts.
```
--display  'Space Grotesk', 'Segoe UI', system-ui, sans-serif
--sans     'IBM Plex Sans', 'Segoe UI', system-ui, -apple-system, sans-serif
--mono     'IBM Plex Mono', ui-monospace, 'Cascadia Mono', Consolas, monospace
```

Space Grotesk (technical grotesque) carries every headline and the name; it reads
well huge and in motion. IBM Plex Mono carries labels, years, chips, readouts and
the marquee — the technical register that ties back to the security world. IBM
Plex Sans carries body copy.

- Name: `clamp(52px, 12.5vw, 190px)`, weight 700, `letter-spacing: -0.035em`
- Section headings: `clamp(36px, 6vw, 78px)`, weight 600
- Body: 15.5–16px / 1.55–1.6, capped ~74ch
- Labels: 11.5–12px mono, uppercase, `letter-spacing: 0.18–0.22em`
- `font-variant-numeric: tabular-nums` on years, stats and the phone number

---

## 4. Layout

```
--gut   clamp(20px, 6vw, 104px)
--maxw  1320px
--nav-h 66px
```

Centring is done with **padding, not `max-width` + auto margins**, so full-bleed
bands (nav, marquee, `#project`, `#contact`) still reach edge to edge while their
content aligns:
```css
.wrap { padding-inline: max(var(--gut), calc((100% - var(--maxw)) / 2)); }
```

Breakpoints: **720px** (nav collapses to a hamburger; two-column grids become one)
and **860px** (project splits to one column). Stats regrid at 780px and 440px.

One weighted easing curve everywhere: `cubic-bezier(.22, 1, .36, 1)`.

---

## 5. Structure

`#experience` · `#project` · `#skills` · `#education` · `#contact` — IDs preserved
from the previous build so old links and muscle memory still resolve.

Eight distinct layout families, so the page never repeats a section shape:

- **Fixed nav** — brand mark, section links, theme toggle, one "Get in touch"
  button. Gains a blurred background once scrolled. Links collapse into a
  hamburger dropdown below 720px.
- **Hero** — full-height, asymmetric. Eyebrow, huge two-line name with a lime
  full stop, a lede, two CTAs. Node canvas + pointer spotlight + corner gradients
  behind. Content is capped so it always fits one viewport.
- **Stat strip** — four real facts (Security+, 30 client environments, 3 roles,
  BSc), divider-separated, numbers count up on reveal.
- **Marquee** — one continuous mono strip of the real toolset. Pauses on hover and
  under reduced motion.
- **Experience** — three `<details>` rows: year, big role title, org, expand plus.
  First open. Editorial list, not cards.
- **Project** — full-bleed `--bg-1` band. Big CloudChain title, sub, four points,
  a pickable tech stack with a readout, and a live "chained node ring" canvas.
- **Skills** — six category groups in a two-column grid, one sticky `$ man <skill>`
  readout above them.
- **Education** — a two-node timeline rail.
- **Contact** — centred closing moment: "Get in touch", the email at display
  scale, then phone / LinkedIn / GitHub / location.
- **Footer** — colophon only.

---

## 6. Signature components

**Kinetic name reveal** — each line of the name sits in an `overflow: hidden`
clip and rises from 110% on load via the Web Animations API, timed to fire after
the display font is ready so the mask lines up on final glyphs.

**Node fields (canvas)** — the hero runs a drifting node/link network themed to
`--net`; the project runs a rotating "chain ring" with a travelling packet. Both
pause off-screen, on tab-hide, and under reduced motion, re-read their colour when
the theme flips, and remove themselves rather than cost the page if anything
throws. The hero canvas re-sizes on load / resize / ResizeObserver so its backing
store can never drift from its box.

**Pointer spotlight** — a radial `--glow` follows the cursor across the hero
background. The native cursor is untouched (no custom-cursor tell); pointer-only,
reduced-motion off.

**Magnetic buttons** — `.magnetic` controls translate toward the cursor via rAF
and transform (never React/`useState`, never per-frame layout). Pointer-only,
reduced-motion off.

**Readout pattern** — used twice (skills, project). Hover previews, click pins,
leaving restores the pin, opens on the first item so it is never empty. Reserved
height so swapping entries never shifts the page.

**Ink-fill controls** — buttons, chips and the plus toggle fill or lift on hover
with the accent; on the accent-ink pairing the text stays legible (13.5:1).

**Grain** — a fixed `feTurbulence` overlay, `pointer-events: none`, `overlay`
blend on dark and `multiply` on light. Adds texture, no colour of its own.

---

## 7. Motion

Dials: variance 8 / motion 8 / density 4. Only `transform`, `opacity` and colour
animate. Scroll reveals use `IntersectionObserver` (16–26px rise, staggered),
with an end-of-page sweep so nothing in the last screenful is ever stranded.

`prefers-reduced-motion` collapses everything: no name reveal, no marquee, no
spotlight, no magnetism, no count-up, canvases paint one static frame, reveals are
simply present.

---

## 8. Non-negotiables

1. **Progressive enhancement.** Every JS-dependent state is scoped to a class only
   JS adds (`js-reveal`, `js-anim`, `js-ctx`). Without JS the page renders
   finished: the name is present, the marquee is CSS, `<details>` work natively,
   and each skill group shows its own readout list instead of the single readout.
2. **Reserve height before animating.** Nothing below an animating block moves.
3. **Failsafes.** Every canvas and the reveal observer sit in `try/catch` and fail
   to a finished page, never a broken one.
4. **No invented content.** Every claim traces to the résumé (V04). Zero
   em-dashes and en-dashes in visible copy — checked mechanically before ship.
5. **One accent, one radius system, one theme per page.**

---

## 9. Content sources

Résumé **V04** (`resume.pdf`) is canonical; the site's copy is a rewrite of it,
not a transcription. Every experience bullet, skill readout, the CloudChain
project and both education entries map to V04.

Deliberately **off** the page: home address, referees, Peninsula's "First Class"
(CGPA only). Deliberately **on**: the mobile number, at the owner's explicit
request.

**Naming choices carried over:** the site titles the current role "Operation and
Maintenance Engineer" and the Ensign role "Security Analyst (Internship)".

---

## 10. Files

| file | tracked | purpose |
|---|---|---|
| `index.html` | yes | the entire site |
| `404.html` | yes | dark 404, big `404.` display, section links |
| `resume.pdf` | yes | V04, served by the Résumé button |
| `og.png` | yes | 1200×630 social card, regenerated in the new palette |
| `favicon.svg` | yes | lime node-triangle mark on near-black |
| `favicon.png` | yes | 64px raster fallback |

`main` deploys to Pages from root.

---

## 11. Open items

- No `robots.txt` or `sitemap.xml`.
- Python is still absent from the skills, though named for some L2 support roles.
- The OG card and favicon are generated from the palette; if the accent hue ever
  changes, regenerate both (`--accent` → `--net` rgb must stay in sync).
- Print stylesheet strips the cinematic chrome but has never been checked against
  a real printer.
