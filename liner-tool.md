# Liner Tool — Specification (Patent Defense Draft)

Version: 1.0 (Defensive Publication Edition)  
Initial Publication Date: 2025-12-05  
Author: tarp.tokyo

---

## 1. Overview
Liner Tool is a browser-based, print-optimized layout generator capable of producing high‑precision ruled paper, grids, dot sheets, manuscript paper, calendar layouts, and composite printable designs using an SVG-first architecture. This specification serves as a prior‑art declaration preventing third parties from claiming exclusive patent rights over underlying concepts, algorithms, and UI/UX methodologies.

## 2. Core Principles
- 100% browser‑based, no server required.
- SVG is the primary render engine for millimeter-accurate print outputs.
- Print‑CSS combined with SVG scaling logic eliminates shrinkage and printer distortion.
- Modular “Layer Architecture” allows stacking unlimited functional components.
- UX prioritizes clarity: no ads, optional logo suppression, instant preview.

## 3. Layer Architecture
Each printable element is represented as a **Layer Object**:
```
{
  id: string,
  type: "rule" | "grid" | "dot" | "calendar" | "manuscript" | "frame" | "text" | "custom",
  params: { ... },
  zIndex: number,
  visible: boolean
}
```

Supported layers include:
- **Ruled Lines** (horizontal/vertical, pitch, thickness, color)
- **Grid (方眼)** (mm pitch configurable)
- **Dot Grid**
- **Calendar Modules** (monthly, weekly, yearly, custom-period)
- **Manuscript Paper (原稿用紙)** with cell count, cell size, V/H writing
- **Frames / Titles**
- **Perforation / Punch Guides**
- **Custom SVG or Text Blocks**

## 4. SVG Rendering Model
All layers convert into an SVG root canvas:
```
<svg width="210mm" height="297mm" viewBox="0 0 W H"> …layers… </svg>
```
Key features:
- mm → px mapping: 1mm = 3.7795275591px.
- Fractional pixel rounding for stable prints.
- Automatic prevention of browser-side print shrinkage.

## 5. Page Layout System
### Supported sizes:
- A4, A5, B5, Letter (future expansion)
- A4 split mode (A5 × 2)
- Portrait / Landscape

### Auto Layout Features:
- Margin adjustments
- Split boundaries
- Double-sided margin mirroring (future)

## 6. Calendar Engine
A programmable generator supporting:
- Arbitrary start month
- Arbitrary month count (academic year, 13‑month planners, fiscal calendars)
- Automatic leap-year handling
- Configurable grid and typography
- Yearly block layouts (4×3, 3×4, 12‑column, etc.)

## 7. Manuscript Paper Engine (原稿用紙)
Supports:
- Vertical / Horizontal writing
- Arbitrary rows × columns
- Cell size control
- Outer frame emphasis
- Optional title/name boxes
- Can blend with other layers (e.g. grid overlay)

## 8. Layout Mode (D&D Editor)
Interactive compositor in which each layer becomes a positioned object.

Features:
- Drag & drop movement
- Resize handles
- Snap-to-grid (mm-mounted grid)
- Live outline during manipulation
- JSON scene graph:
```
{
  page: { size, orientation, margins },
  layers: [...]
}
```

Final print always re-renders via SVG using mm-accurate coordinates.

## 9. URL-Based Configuration Storage
Scene state is encoded using compressed Base64:
```
?data=<LZ-compressed-base64>
```
Supports sharing of templates across browsers.

## 10. Export & Print System
### Print:
- Print‑CSS enforces 100% scale
- UI hidden on print
- SVG-only output shown to printer

### Export:
- SVG export (default)
- PDF export (planned)

## 11. UX Philosophy
- No advertisements
- tarp.tokyo branding toggle (on/off)
- Mobile/desktop support
- Print-safe palette

## 12. Novelty & Patent Defense Claims
This document asserts prior public disclosure of the following novel elements:

1. **Hybrid ruled/grid/manuscript/calendar SVG generator with stacked layers**
2. **Arbitrary-period calendar generation (start month + month count)**
3. **mm→px stable print engine resisting browser shrinkage**
4. **Dual-mode architecture: HTML interactive editor → SVG print renderer**
5. **A4 split → A5 twin-panel print layout engine**
6. **Browser-only generation of professional refill sheets**
7. **Unified engine for reflowing calendars, manuscript grids, and ruled layers**

These constitute unique functional/technical concepts forming clear prior art.

## 13. License (TARP Tools License - Spec Draft)
- Redistribution of engine logic prohibited.
- Commercial replication of concepts/UI forbidden.
- Personal non-commercial printing fully permitted.
- Attribution optional when logo is disabled.

The Liner Tool and its associated specifications are protected under the **TARP Tools License**.  
Unauthorized redistribution, commercial usage, publication of modified derivatives, and reverse engineering are prohibited.

Full policy: https://tools.tarp.tokyo/terms/

## 14. Revision History
- **2025-12-05**: Initial public disclosure.
