---
name: nice-pdf
description: Generate visually polished PDF documents (reports, fiches, dashboards, one-pagers, invoices, study guides, posters) by writing HTML+CSS and rendering with WeasyPrint. Use this skill whenever the user wants a "nice", "polished", "pretty", "designed", or "good-looking" PDF, or when the deliverable is a layout-heavy document with colored sections, side-by-side comparisons, callout boxes, badges, tables, or grids — even if the user just says "make me a PDF". Also use when iterating on the visual design of a previously-generated PDF. Do NOT use this skill for filling out existing PDF forms, merging/splitting/rotating PDFs, or extracting text from PDFs — use a different PDF tool for those.
---

# Nice PDF (HTML + CSS → WeasyPrint)

A skill for producing visually polished PDFs by writing HTML+CSS and converting with WeasyPrint. The HTML+CSS approach is much faster to iterate on than building PDFs programmatically (e.g. ReportLab) for any document where layout, color, and typography matter.

## When to use this approach

Use HTML+CSS+WeasyPrint when the output is **layout-driven**:
- Revision sheets, study guides, cheat sheets
- One-pagers, executive summaries, reports
- Invoices, quotes, statements
- Dashboards, KPI snapshots
- Posters, infographics, recipe cards
- Anything with colored callout boxes, side-by-side comparisons, vocab grids, timelines, badges

Don't use this for: filling existing PDF forms, merging/splitting/rotating PDFs, extracting text or tables from a PDF — those are different tools.

## High-level workflow

1. **Write a single HTML file** with embedded `<style>` (no external CSS, no external images unless absolutely needed — keep it self-contained for portability).
2. **Render to PDF** with one line of WeasyPrint.
3. **Iterate**: open the HTML in a browser to preview quickly, then re-render the PDF when satisfied.

Keep everything in one HTML file. It's tempting to split CSS out, but for a one-shot deliverable the friction isn't worth it.

## Rendering

Single line, in a script or a one-off `python3 -c`:

```python
from weasyprint import HTML
HTML("input.html").write_pdf("output.pdf")
```

For URLs or stylesheets:
```python
HTML("input.html").write_pdf("output.pdf", stylesheets=["extra.css"])
```

## HTML template (start every document from this skeleton)

This skeleton bakes in the things that consistently make print PDFs look good: A4 page setup, sane base typography, and a clean color system. Adapt it to the document — don't keep classes you don't use.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Document title</title>
<style>
  /* === PAGE === */
  @page {
    size: A4;            /* or "Letter" for US */
    margin: 1.5cm;
  }

  /* === BASE TYPOGRAPHY === */
  body {
    font-family: 'Helvetica', 'Arial', sans-serif;
    font-size: 11pt;     /* 10–11pt prints well; 12pt for elderly readers */
    line-height: 1.45;
    color: #222;         /* never pure black — softer on the eye */
  }

  /* === HEADINGS === */
  h1 {
    color: #c0392b;
    border-bottom: 3px solid #c0392b;
    padding-bottom: 6px;
    font-size: 20pt;
    margin-bottom: 4px;
  }
  h2 {
    color: #2c3e50;
    background: #ecf0f1;
    padding: 6px 10px;
    border-left: 5px solid #e67e22;
    margin-top: 18px;
    font-size: 14pt;
  }
  h3 {
    color: #16a085;
    margin-top: 12px;
    font-size: 12pt;
  }

  /* === CALLOUT BOXES (use generously — they break up text and signal hierarchy) === */
  .key      { background:#eaf6fb; border-left:4px solid #3498db; padding:8px 12px; margin:8px 0; border-radius:3px; }
  .warning  { background:#fdedec; border-left:4px solid #e74c3c; padding:6px 12px; margin:8px 0; border-radius:3px; }
  .definition { background:#fef9e7; border-left:4px solid #f1c40f; padding:6px 12px; margin:8px 0; border-radius:3px; }
  .success  { background:#e8f8f0; border-left:4px solid #27ae60; padding:6px 12px; margin:8px 0; border-radius:3px; }

  /* === TABLES === */
  table { width:100%; border-collapse:collapse; margin:8px 0; font-size:10pt; }
  th    { background:#34495e; color:white; padding:6px; text-align:left; }
  td    { border:1px solid #bdc3c7; padding:5px 8px; vertical-align:top; }
  tr:nth-child(even) td { background:#f8f9fa; }

  /* === SIDE-BY-SIDE COMPARISON === */
  .compare { display:grid; grid-template-columns:1fr 1fr; gap:10px; }
  .compare > div { padding:8px; border-radius:5px; }

  /* === CARD GRID (vocab, KPIs, etc.) === */
  .card-grid { display:grid; grid-template-columns:1fr 1fr; gap:8px; margin:8px 0; }
  .card { background:#f4f6f7; border:1px solid #d5dbdb; padding:6px 10px; border-radius:4px; }

  /* === FOOTER === */
  .footer {
    margin-top:20px; padding-top:8px;
    border-top:1px solid #ecf0f1;
    text-align:center; color:#95a5a6; font-size:9pt;
  }

  /* === PRINT CONTROL === */
  .page-break { page-break-after: always; }
  .avoid-break { page-break-inside: avoid; }
</style>
</head>
<body>

<h1>Document title</h1>
<p style="color:#7f8c8d; font-style:italic; margin-top:0;">Subtitle / context</p>

<h2>Section</h2>
<p>Body text…</p>

<div class="key">A key takeaway lives here.</div>

<div class="footer">Generated <span>YYYY-MM-DD</span></div>

</body>
</html>
```

## Design principles (what makes the output "nice")

1. **Visual hierarchy via color, not weight alone.** Colored left borders on callout boxes are the single highest-leverage move — they let a reader scan a 3-page document in 5 seconds.
2. **Soft colors, not pure black/white.** Body text `#222`, gray sub-text `#7f8c8d`, light backgrounds `#f4f6f7`–`#fef9e7`. Reserved saturated colors (red `#c0392b`, blue `#3498db`) for accents and headings.
3. **CSS Grid for any 2-column-ish layout.** Comparisons, vocab cards, KPI tiles — all just `display:grid`. Don't fight with floats or tables for layout.
4. **Restraint with fonts.** One sans-serif family throughout. Vary size and color, not face. WeasyPrint can use web fonts via `@font-face` if needed, but Helvetica/Arial is fine for most things.
5. **Emoji are valid design elements.** Section markers like 📅 🗺️ 👑 add scannability and warmth, especially for educational, recipe, or consumer-facing documents. Skip them for formal/legal/financial documents.
6. **Consistent spacing.** `margin: 8px 0` on callouts, `margin-top: 18px` on `h2` — small consistent rhythms matter more than absolute values.
7. **Page-break control.** Wrap any block that must not be split (a table, a card) in `class="avoid-break"`. Force a break with `class="page-break"`.

## Reusable component patterns

### Timeline / chronology block

```html
<div style="background:#d6eaf8; padding:10px; border-radius:5px; margin:10px 0; font-size:10pt;">
  <strong>2020</strong> — event one<br>
  <strong>2022</strong> — event two<br>
  <strong>2024</strong> — event three
</div>
```

### Two-column comparison

```html
<div class="compare">
  <div style="background:#fdf2e9; border:1px solid #e67e22;">
    <h3 style="margin-top:0;">Option A</h3>
    <ul><li>…</li></ul>
  </div>
  <div style="background:#fef9e7; border:1px solid #f39c12;">
    <h3 style="margin-top:0;">Option B</h3>
    <ul><li>…</li></ul>
  </div>
</div>
```

### Card grid (vocabulary, KPIs, features)

```html
<div class="card-grid">
  <div class="card"><strong>Term 1</strong>: definition.</div>
  <div class="card"><strong>Term 2</strong>: definition.</div>
  <div class="card"><strong>Term 3</strong>: definition.</div>
  <div class="card"><strong>Term 4</strong>: definition.</div>
</div>
```

### Recap table

```html
<table>
  <tr><th>Field</th><th>Where</th><th>When</th><th>Notes</th></tr>
  <tr><td>Row 1</td><td>…</td><td>…</td><td>…</td></tr>
</table>
```

## Color palettes that work

Pick one palette per document. Don't mix.

**Educational / friendly** (used for the example fiche above):
- Primary `#c0392b` (red), Secondary `#16a085` (teal), Accent `#e67e22` (orange)
- Backgrounds: `#fef9e7` (yellow), `#eaf6fb` (blue), `#fdedec` (red), `#e8f8f0` (green)

**Corporate / report**:
- Primary `#1f3a5f` (navy), Secondary `#5a6c7d` (slate), Accent `#c9a961` (gold)
- Backgrounds: `#f5f5f0`, `#eef2f7`

**Modern / SaaS**:
- Primary `#6366f1` (indigo), Secondary `#06b6d4` (cyan), Accent `#f59e0b` (amber)
- Backgrounds: `#f9fafb`, `#eef2ff`

## Rendering script template

Drop this into a file like `render.py` next to the HTML:

```python
#!/usr/bin/env python3
"""Render input.html to output.pdf."""
import sys
from pathlib import Path
from weasyprint import HTML

src = Path(sys.argv[1] if len(sys.argv) > 1 else "input.html")
dst = src.with_suffix(".pdf")
HTML(str(src)).write_pdf(str(dst))
print(f"Wrote {dst}")
```

Run with `python render.py myfile.html`.

## Iteration tips

- **Preview in a browser first.** Open the HTML directly — every modern browser renders it close enough to WeasyPrint's output that you can iterate fast on layout/colors without re-rendering the PDF every time.
- **Render the PDF only when you're happy with the HTML preview.** Catches the few WeasyPrint-specific quirks (mainly around `@page` rules and some advanced flexbox) early but late.
- **If something looks wrong in the PDF but right in the browser**, the usual culprits are: missing `@page` size, web fonts that didn't load, or `position: fixed` (don't use it — use `@page` margin boxes instead for headers/footers).

## Common pitfalls

- **Using external image URLs**: WeasyPrint will try to fetch them at render time. If offline or behind a firewall, embed images as base64 data URIs, or use a local file path.
- **Pure black text on pure white**: looks harsh in print. Use `#222` on `#fff` or `#fafafa`.
- **Tiny font sizes to fit content**: if you're below 9pt, the document is too dense — cut content or split across pages instead.
- **Forgetting `@page` rules**: without them you get default Letter sizing with thin margins, which rarely matches what you want.
- **Trying to use JavaScript**: WeasyPrint doesn't execute JS. All content must be in the static HTML.

## Output destination

Write the final PDF where the user can find it, under /opt/projects/docs/, otherwise write next to the source HTML or wherever the user specified. Use a descriptive filename based on the document's date and contents, not generic names like `output.pdf`.
