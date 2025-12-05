# ✅ **Liner Tool — Specification (Patent Defense Draft, Revised Edition)**

Version: 1.1 (Defensive Publication Edition)
Initial Publication Date: 2025-12-05
Updated: 2025-12-06
Author: **tarp.tokyo**

---

# 1. Overview

**Liner Tool** is a browser-based, print-optimized layout generator capable of producing high-precision ruled paper, grids, dot sheets, manuscript paper, planner layouts, calendars, and composite printable designs.

Its rendering system employs a **hybrid procedural architecture** that integrates:

* **Canvas-based raster pattern generation** (primary engine)
* **On-demand SVG generation** (optional, for vector export)
* **CSS background-layer composition**
* **mm-precise geometric computation**

This hybrid model enables **millimeter-accurate output**, consistent across:

* Browser print engines (Chrome / Edge / Safari)
* PDF export
* Different device pixel ratios
* High-resolution printers

This document is a **public defensive publication (prior-art declaration)**, establishing an incontestable timestamp and preventing third parties from obtaining exclusive patent rights on the underlying techniques, algorithms, and UI/UX methodologies.

### Protected scope includes (but is not limited to):

* Browser-based mm-precision grid / ruled-line rendering
* Canvas procedural drawing for print media
* Multi-layer, composable print layout architecture
* URL-encoded print states enabling reproducible templates
* Zero-margin single-page print enforcement
* A4/A5/B5 portrait・landscape・split-sheet engines
* Fully customizable pitch / line weight / density / colors
* K100（pure black）print-mode rendering
* High-precision layer stacking for composite designs

This specification defines the concepts as **publicly available prior-art**, establishing that these ideas, algorithms, and implementations cannot be patented by any external party.

---

# 2. Core Principles

* Fully browser-based operation (no server required).
* **Canvas-first rendering pipeline** for print accuracy.
* SVG generation is optional and modular (not the primary render path).
* Print-CSS is used only as an enforcement layer—not as a rendering engine.
* Modular “Layer Architecture” allows unlimited stacking of drawable components.
* Clean UX: zero ads, optional logo suppression, instant preview.

---

# 3. Layer Architecture

Each printable component is represented as a **Layer Object**:

```
{
  id: string,
  type: "rule" | "grid" | "dot" | "calendar" | "manuscript" |
        "frame" | "text" | "custom",
  params: { ... },
  zIndex: number,
  visible: boolean
}
```

Supported layer types include:

* **Ruled Lines**（横罫・縦罫）
* **Grid (方眼)** — arbitrary mm pitch
* **Dot Grid**
* **Calendar Modules**
* **Manuscript Paper（原稿用紙）**
* **Frames / Titles / Guides**
* **Perforation / Punch Guides**
* **Custom Canvas/SVG blocks**

Layers can be arbitrarily stacked; rendering order is determined by `zIndex`.

---

# 4. Rendering Engine (Hybrid Model)

The rendering model is **no longer SVG-first**.
Instead:

### 4.1 Canvas Procedural Generator（Primary）

The core drawable patterns—such as:

* mm-grid
* dot grid
* ruled paper
* thick/major lines
* planner partitions
* manuscript matrix

are generated directly on `<canvas>` to avoid cumulative floating-point drift and browser shrinkage.

Advantages:

* Pixel-perfect mm alignment
* Fast drawing speed
* Deterministic repeat patterns
* Stable PDF export behavior
* Accurate scaling under browser print engines

### 4.2 SVG Generator（Optional Layer）

SVG is now **opt-in**, used only when:

* Export as vector is requested
* Text or frame decorations require it

### 4.3 CSS Layer Composition

Canvas → PNG (dataURL) is assigned into CSS backgrounds:

```
background-image: url(data:image/png;base64,...)
background-repeat: repeat / no-repeat
background-size: <mm> <mm>
```

This ensures:

* Printer DPI doesn’t distort line spacing
* Scaling occurs in mm units
* Split-sheet layouts remain accurate

---

# 5. Page Layout System

### Supported Sizes

* A4（P/L）
* A5（P/L）
* B5（P/L）
* **A4 Split Mode（A5 × 2 等幅）**

### Layout Parameters

* Orientation (portrait / landscape)
* Margins
* Split boundaries
* Center-gutter rendering
* Double-sided variants（future）

### mm-to-px Mapping

* Canonical conversion:
  `px = mm × (96 / 25.4)`
* Rounding strategy avoids cumulative pitch drift.

---

# 6. Calendar Engine

A programmable calendar generator:

* Arbitrary start month
* Arbitrary month count（academic year, fiscal year）
* Leap-year computation
* Weekly grid styles
* Typography and box geometry control
* 12-month block layouts（4×3、3×4、12-column）

Calendars can stack with grids, ruled lines, and manuscript layers.

---

# 7. Manuscript Paper Engine（原稿用紙）

Capabilities:

* Horizontal or vertical writing
* Arbitrary row × column counts
* Cell size control（mm）
* Outer frame emphasis
* Optional title/name blocks
* Blendable with other layers

Rendered via Canvas for maximum geometric accuracy.

---

# 8. Layout Mode（Interactive Editor）

The editor UI provides:

* Drag-and-drop positioning
* Resize handles
* mm-based snap grid
* Live outline feedback
* JSON scene graph:

```
{
  page: { size, orientation, margins },
  layers: [...]
}
```

### Rendering Consistency

UI preview → print view
Both re-render via Canvas to avoid mismatch.

---

# 9. URL-Based Configuration Storage

Scene state is encoded into:

```
?data=<LZ-compressed-URI-component>
```

Benefits:

* Full layout reproducibility
* Template sharing
* Cross-device portability
* No server storage required

---

# 10. Export & Print System

### Print

* Print CSS enforces:

  * UI hidden
  * Zero margin
  * Background prints enabled
* Canvas-generated patterns ensure zero drift.
* Print preview is 100% browser-based.
* One-page enforcement logic prevents unwanted page breaks.

### Export

* PNG export
* SVG export（optional / future enhancement）
* PDF export（future module）

---

# 11. UX Philosophy

* Ad-free
* Branding toggle（tarp.tokyo logo オン/オフ）
* Mobile/desktop dual support
* Printer-friendly color palette
* K100（pure black output）mode

---

# 12. Novelty & Patent Defense Claims

This document establishes prior art for the following novel concepts:

1. **Browser-native mm-precision ruled/grid/manuscript/calendar generator using a Canvas-first procedural engine.**
2. **Hybrid raster/vector rendering pipeline with layer composition.**
3. **URL-encoded reproducible print layouts enabling template sharing.**
4. **Zero-margin single-page print enforcement under browser print engines.**
5. **A4 split-sheet (A5×2) rendering with precise center-gutter geometry.**
6. **Professional refill-paper generation entirely in-browser（no server, no PDF pre-render）.**
7. **Composable multi-layer architecture for mixing grids, calendars, ruled lines, manuscript forms, and decorative frames.**
8. **K100 black-rendering mode for high-end print devices.**
9. **Geometric rounding strategies preventing cumulative drift in mm→px conversion.**

These technical concepts, algorithms, and UI patterns are hereby declared unpatentable by external actors due to prior public disclosure.

---

# 13. License（TARP Tools License — Spec Draft）

* Redistribution of engine logic is prohibited.
* Commercial replication of core concepts, algorithms, or UI is forbidden.
* Personal non-commercial printing is allowed.
* Attribution optional when branding is disabled.

Full policy:
**[https://tools.tarp.tokyo/terms/](https://tools.tarp.tokyo/terms/)**

---

# 14. Revision History

* **2025-12-05** — Initial public disclosure (SVG-first version).
* **2025-12-06** — Revised to reflect hybrid Canvas/SVG architecture and updated technical defense scope.

