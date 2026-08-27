# DESIGN.md — dcyxboi.github.io

Personal portfolio for **Danny Chan Yi Xiang** — Cybersecurity / Data Center Engineer,
Petaling Jaya, Malaysia. Audience: recruiters and hiring managers for security
operations, application support and infrastructure roles.

- **Live:** https://dcyxboi.github.io
- **Repo:** https://github.com/DCYXboi/dcyxboi.github.io (GitHub Pages, `main` branch, root)
- **Stack:** one hand-written `index.html`. No framework, no build step, no dependencies.
  Vanilla CSS in a single `<style>`, vanilla JS in a single `<script>`.

---

## 1. Direction

A **command line**. The page opens on a terminal and closes on one; everything
between them is set as if printed. This was chosen deliberately over the generic
"premium SaaS" look and should not drift toward it.

**Reject on sight:** glassmorphism, `backdrop-filter` on content, rounded corners
(the page is square throughout — only the cursor and focus ring are exceptions, and
they are square too), pill buttons, purple/blue gradients, Inter/Roboto/Arial,
drop shadows, three-equal-card feature rows, spring-bounce overshoot.

**Already refused, on purpose:** Vercel's copywriting house style (title case, "&"
for "and") — that is Vercel's voice, not this site's. Design-system clones from
extracted DESIGN.md libraries — reference the structure, never wear the identity.

---

## 2. Tokens

Every colour is oklch. Two themes, driven by `prefers-color-scheme` with a
`[data-theme]` override. **Never define a colour only inside a media query** — the
bare `:root` carries the full light palette.

### Light
```
--paper       oklch(0.958 0.004 125)   page ground
--panel       oklch(0.925 0.005 125)
--ink         oklch(0.225 0.008 125)   body text
--ink-soft    oklch(0.455 0.008 125)   secondary text
--ink-faint   oklch(0.52  0.006 125)   labels, meta   (4.85:1 — do not lighten)
--rule        oklch(0.865 0.005 125)   hairlines
--rule-firm   oklch(0.225 0.008 125)
--accent      oklch(0.435 0.115 22)    oxblood
--accent-dim  oklch(0.78  0.045 22)
--net         123, 46, 51              rgb triplet for canvas
```

### Dark
```
--paper       oklch(0.195 0.007 125)
--panel       oklch(0.255 0.008 125)
--ink         oklch(0.928 0.004 125)
--ink-soft    oklch(0.715 0.006 125)
--ink-faint   oklch(0.62  0.006 125)   (5.05:1 — do not darken)
--rule        oklch(0.325 0.007 125)
--rule-firm   oklch(0.928 0.004 125)
--accent      oklch(0.735 0.105 25)    rose
--accent-dim  oklch(0.44  0.065 25)
--net         208, 140, 134
```

**One accent only.** Neutrals are all tinted to hue 125 (a cool green-grey) — never
mix a warm grey in.

`--net` and the pixel palette exist as rgb triplets because `canvas` `fillStyle`
cannot be trusted to parse `oklch()`.

### Measured contrast — treat as floors
| pair | light | dark |
|---|---|---|
| ink on paper | 15.22 | 14.87 |
| ink-faint on paper | 4.85 | 5.05 |
| paper on accent | 7.46 | 7.49 |

`--ink-faint` sits on 10.5–15px text, so it is bound by the 4.5:1 normal-text floor,
not the 3:1 large-text one. It failed at 3.44 (light) before being corrected.

---

## 3. Type

Loaded from Google Fonts — the only external host the page touches.

```
--serif  'Instrument Serif', 'Iowan Old Style', Georgia, 'Times New Roman', serif
--sans   'IBM Plex Sans', 'Segoe UI', system-ui, -apple-system, sans-serif
--mono   'IBM Plex Mono', ui-monospace, 'Cascadia Mono', Consolas, monospace
```

The tension is the point: a high-contrast literary serif against a technical
grotesque. Serif for the name and section headings only. Mono carries the terminals,
labels, dates, status and chips. Sans carries body copy.

- Name: `clamp(42px, 7.6vw, 100px)`, centred, `letter-spacing: -0.02em`
- Section headings: `clamp(30px, 4.2vw, 46px)` serif, with an accent hairline beside them
- Body: 16px / 1.62, capped at 62–70ch
- Labels: 11px mono, uppercase, `letter-spacing: 0.15em`
- `font-variant-numeric: tabular-nums` on dates, CGPA, status and phone

---

## 4. Layout

```
--gut   clamp(20px, 6vw, 96px)
--col   190px      left meta column (dates, labels) — collapses to 0 below 860px
--maxw  1240px
```

Centring is done with **padding, not `max-width` + auto margins**:

```css
.wrap { padding-inline: max(var(--gut), calc((100% - var(--maxw)) / 2)); }
```

This keeps elements full width so the sticky nav, the accent project panel, the
fact strip and the footer still bleed edge to edge while their content aligns. It
also avoids the scrollbar drift a `100vw` calculation introduces. At 1920px this
yields a 1240px centred column; below ~1440px the gutter takes over.

Single breakpoint: **860px**. Below it `--col` goes to 0 and every two-column grid
becomes one.

---

## 5. Structure

`#experience` · `#project` · `#skills` · `#education` · `#contact`

- **Boot screen** — full-screen overlay, ~1.4s log, then fades and hands to the hero.
- **Hero** — centred serif name; terminal left (`danny@portfolio ~`), `[ SYSTEM ONLINE ]`
  status panel right with four boxed links beneath it; drifting node canvas behind.
- **Sticky nav** — four sections, mono, active state tracked by scroll position.
- **Fact strip** — certification / degree / languages, three cells with border dividers.
- **Experience** — three `<details>` rows, date in the left column, first one open.
- **Project** — the one full-bleed accent panel; inverts in dark mode. Pickable
  tech stack with a readout.
- **Skills** — six category rows of chips, one sticky `$ man <skill>` readout.
- **Education** — timeline rail with a node per entry.
- **Contact** — closing terminal, `$ cat contact.txt`.
- **Footer** — colophon only.

---

## 6. Signature components

**Ink wipe** — a block of accent sweeps up from the baseline and the label flips to
paper as it passes. On nav, role rows, and links. It deliberately **bleeds outside**
the element box (16px each side on a role row). Hover only — never focus, because an
outline hugs the box and the two can never align.

**Focus ring** — `2px solid var(--accent)`, `outline-offset: 2px`, square. On the
accent panel it flips to `--paper`.

**Cursor** — a 10px terminal cell, hotspot centred, drawn as an inline SVG in the
`cursor` property. Fills with accent over anything clickable. Built from a
fill-and-stroke pair, not flat accent, so it survives the accent panel. Reverts to
native on touch.

**Readout pattern** — used twice (Skills, Project). Hover previews, click pins,
leaving restores the pin. Opens showing the first item so it is never empty and never
needs instruction copy. Reserved height so swapping entries does not shift the page.

**Scanlines** — fixed overlay, `repeating-linear-gradient` at 3% black, 1px on / 2px
off, `pointer-events: none`. Adds no colour of its own.

---

## 7. Motion

One curve: `cubic-bezier(.32, .72, 0, 1)` — weighted, never `linear` or `ease-in-out`.
Only `transform`, `opacity` and colour animate.

- **Terminal typing** — 285 per-character spans, hidden by `visibility` so the box is
  full size before a word appears. Commands ~26ms/char, output ~6ms, 150ms beat
  between a command and its output. Any click or key finishes it.
- **Status boot** — each check lands, then resolves ~260ms later; header flips
  `[ SYSTEM BOOT ]` → `[ SYSTEM ONLINE ]`.
- **Scroll reveal** — 16px rise over 620ms via `IntersectionObserver`. Plus a sweep
  that reveals anything left when the page reaches its end, because the observer's
  bottom margin can otherwise strand a block near the document floor forever.
- **Hover** — chips and entries lift 2–4px and press back on `:active`.

`prefers-reduced-motion` skips the boot screen, the typing, the caret blink, the
status sequence and every transform.

---

## 8. Non-negotiables

1. **Progressive enhancement.** Every JS-dependent state is scoped to a class only JS
   adds (`.js-reveal`, `.js-ctx`, `.js-boot`, `.booting`, `.typing`). Without JS the
   page renders finished. The boot overlay is `display: none` until armed — a curtain
   that cannot lift is worse than no curtain.
2. **Reserve height before animating.** Hide with `visibility`, not `display`.
   Nothing below an animating block may move.
3. **Failsafes.** Boot screen, typing and status each have a timeout that completes
   them; the intro sits in `try/catch` and removes itself rather than costing the page.
4. **No invented content.** Every claim traces to the resume. Missing facts get a
   visibly marked placeholder, never a plausible guess.

---

## 9. Content sources

Resume **V04** (`resume.pdf`) is canonical. Earlier versions differ materially —
V03 lacks the Linux/CentOS work, the Go/MySQL project detail and the six skill
categories. The site's copy is a rewrite of V04, not a transcription.

Deliberately **off** the page: home address, referees' names and phone numbers
("references available on request"), and Peninsula's "First Class" (CGPA only).
Deliberately **on**: mobile number, added at the owner's explicit request after the
spam trade-off was raised.

**Naming mismatch to watch:** the site says "Security Analyst (internship)" and omits
"First Class"; the PDF says "Security Analyst Intern" and includes it. Site titles the
role "Data Center Engineer"; the PDF says "Operation & Maintenance Engineer".

---

## 10. Files

| file | tracked | purpose |
|---|---|---|
| `index.html` | yes | the entire site, 79.5 KB |
| `404.html` | yes | terminal-styled, `cd: no such file or directory` |
| `resume.pdf` | yes | V04, served by the Resume button |
| `og.png` | yes | 1200×630 social card, generated from the palette |
| `favicon.svg` | yes | caret + prompt lines |
| `favicon.png` | yes | 32px raster fallback |
| `V04_ Resume_Danny.pdf` | **no** | untracked duplicate of `resume.pdf` |
| `headshot.png` | **no** | untracked; a portrait was tried and removed |

29 commits. `main` deploys to Pages from root.

---

## 11. Open items

- Two untracked files sitting in the repo (above) — commit, delete, or ignore.
- No `robots.txt` or `sitemap.xml`.
- Hero lede was written from V03 and still undersells the V04 infrastructure work.
- Python is absent from the skills — named three times in the L2 Production Support
  role being targeted.
- Print stylesheet exists but has never been checked against a real printer.
