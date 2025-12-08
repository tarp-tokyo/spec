# ✅ **Liner Tool — Specification**

Version: 2.1
Initial Publication Date: 2025-12-05  
Updated: 2025-12-08  
Author: tarp.tokyo  


---

# Liner Tool — Technical Specification  
### Version 2.1 (Integrated Revision)  
### tarp.tokyo — 2025  
### License: TARP Tools License  
https://tools.tarp.tokyo/terms/

---

# 0. Purpose of This Document

This document defines the full technical specification of **Liner Tool v2.1**,  
including all features implemented as of December 2025.  

Version 2.1 supersedes:  

- **v1.1** (initial SVG-based design)  
- **v2.0** (full Canvas architecture / multi-page engine)  

and introduces three major additions:  

1. **Artboard Gutter Specification (page spacing)**  
2. **DPI & Real-Scale Printing Technical Notes**  
3. **Page Number Label UI Specification**

This specification is formatted for:  

- Public GitHub documentation  
- Patent defensive publication  
- Future development reference  
- External technical review  

---

# 1. Overview

Liner Tool is a **millimeter-precision print layout generator** running entirely in the browser.  
It enables users to create notebook paper, ruled paper, grid paper, planner refills,  
and multi-page printable layouts with professional accuracy.  

Key characteristics:  

- **Canvas-based rendering** with zero accumulated error  
- **Multi-page artboard engine** similar to Illustrator/Figma  
- **Independent page types** (grid, ruled, half layouts, calendars, etc.)  
- **Real-scale printing** unaffected by browser DPI differences  
- **Compressed URL save system** (LZString + Base64)  
- **Drag-and-drop page ordering**  
- **Beautiful stationery-inspired UI design**

This tool targets designers, notebook enthusiasts, students, engineers,  
and creators who require *publisher-grade* print precision in a simple web interface.  

---

# 2. Core Architecture

---

## 2.1 Canvas High-Precision Rendering Engine

Liner Tool v2.x uses **100% Canvas-based rendering** (no SVG), providing:  

- Exact physical sizes (A4, A5, B5, etc.)  
- Millimeter-driven rendering (mm → px conversion)  
- Ultra-sharp subpixel lines (0.5px possible)  
- Zero browser-dependent distortion  
- Predictable printing output  
- Perfect consistency across platforms (Windows, macOS, iOS, Android)  

### Patent-relevant points:
- Real-scale printable layout generated entirely by Canvas  
- Algorithm for subpixel grid/ruled-line rendering with mm-level precision  

---

## 2.2 Multi-Page Artboard Engine

All pages are managed inside a `scene` object:  

```js
scene = {
  version: 2,
  pages: [
    {
      id: string,
      type: "grid" | "ruled" | "calendar" | ...,
      layout: "full" | "half-left" | "half-right",
      settings: { ... }
    }
  ]
}
````

### Features:

* Multiple pages displayed visually on a unified Canvas  
* Each page maintains its own type, settings, and layout  
* Artboard-like editing experience (Illustrator/Figma style)  
* Drag-and-drop reordering updates the printing order in real time  
* Individual page selection syncs with the inspector UI  

### Patent-relevant:

**A browser-based multi-artboard layout engine whose order directly controls the print output sequence.**

---

# 3. UI / UX Architecture

---

## 3.1 TARP Tools Dark × Stationery Flat UI 

The global theme uses:  

* Dark navy backgrounds (#0f172b / #1e293b)  
* Soft gray text for "stationery aesthetics"  
* Cyan highlight for selections  
* Tailwind v4 custom design tokens  
* Input components styled for clarity and minimalism  

---

## 3.2 Page Management UI

Features include:  

* “Add Page” panel with selectable page types (Grid, Ruled, Half Layouts, etc.)  
* Page list rendered as cards:

```
📄 Page 1 — Full  
📄 Page 2 — Half Left  
📄 Page 3 — Half Right  
```

* Delete button per page  
* Page selection highlighting  
* Drag-and-drop ordering (via @dnd-kit/core)  
* Full synchronization between UI and Canvas  

---

## 3.3 **Page Number Label UI** (★ New in v2.1)

### Display Rules:

| Number of Pages | Page Number Label  |  
| --------------- | ------------------ |  
| 1 page          | Hidden             |  
| 2+ pages        | Shown on all pages |  

### Positioning:

* Absolutely positioned at the **top-right corner** of each page  
* Rendered only in the UI (excluded from print PNG)  
* Style blends with the dark theme while remaining readable  
* Cyan border and label work together to clarify selection  

### Purpose:

* Allows multi-page identification similar to Illustrator/Figma  
* Greatly improves usability when working with 3+ pages  
 
---

## 3.4 **Artboard Gutter Specification** (★ New in v2.1)

Defines spacing between pages when displayed on the Canvas.  

### Default:

```
artboardGutter = 24px  
```

### Behavior:

* Horizontal spacing applied uniformly  
* After ~5 pages, layout wraps to a second row  
* Gutter remains consistent even when wrapping  
* Ensures visual separation and selection clarity  

### Future Expansion:

* User-configurable gutter spacing  
* Optional layout guides  

---

# 4. Page Types

### 4.1 Grid Paper

* Pitch (mm)  
* Thin/Thick lines  
* Thick step interval  
* Line color  
* Pure black (K100) mode  

### 4.2 Ruled Paper

* Line spacing (mm)  
* Left margin (legal-pad style)  
* Auto line count  
* Pure black mode

### 4.3 Half Layouts

* `half-left` and `half-right`  
* A4 → A5×2 layout logic  
* Independent rendering of left/right side  
* Extremely flexible for planners and split layouts

### Patent point:

A “half-page” rendering model that draws only selected portions of the sheet.  

---

# 5. Print Engine

---

## 5.1 Multi-Page PNG Output

* Each page is rendered into a PNG before printing  
* Eliminates all browser print inconsistencies  
* Guarantees identical output across OS/browser  
* Enables PDF export via browser print → PDF

---

## 5.2 Duplex Print Optimization

* Zero-margin rendering ensures alignment  
* Prevents backside offset  
* Ideal for notebook/booklet production

---

## 5.3 Phantom Blank Page Prevention

* Ensures no “mystery blank pages”  
* Common printing issue fully eliminated

---

## 5.4 Mobile Consistency

* iOS Safari / Android Chrome produce identical prints  
* Unique among browser-based printing tools

---

## 5.5 **DPI & Real-Scale Printing Notes** (★ New in v2.1)

Although browsers use **96dpi CSS pixel density**,  
Liner Tool prints with **true physical dimensions**, not raster DPI.

### Key Principles:

* 1 CSS pixel = 1/96 inch  
* mm → px conversion ensures exact real-world scale  
* DPI does **not** affect line sharpness (unlike photos)  
* Canvas line rendering ≈ vector-level clarity  
* Subpixel lines (0.5px) provide extremely crisp output

### Why DPI does not degrade print quality:

* Layout consists of geometric lines, not images  
* Browser prints apply dimension scaling, not pixel scaling  
* PNG screenshots remain sharp because lines have no raster detail

### Optional future extension:

* 2×/3× DPI Canvas rendering  
* Requires careful browser scaling to avoid blurring  

Current method (v2.1) remains the optimal approach.

---

# 6. Data Save & URL Sharing

---

## 6.1 Compressed URL Save System

* Uses LZString → Base64  
* Saves full page structures inside `?data=`  
* No backend required  
* Shareable and persistent

---

## 6.2 Versioned Scene Data

* `version: 2` (with auto-migration)  
* Future-proof for calendars and decorative layouts  

---

## 6.3 Short URL API

```
POST https://s.tarp.tokyo/api/shorten  
```

* Saves compressed scene data in a database  
* Generates a short, user-friendly share link  

---

# 7. Branding & Presentation

* TARP Tools unified footer (toggleable)  
* Pure black K100 lines for professional printing  
* Dark stationery aesthetic across the UI

---

# 8. Patent-Relevant Features

### Unique elements introduced in v2.1:  

1. **Multi-artboard layout with programmable gutters**  
2. **Page number label system synchronized with UI selection**  
3. **DPI-independent real-scale printing architecture**  

### Continuing from earlier versions:

* Canvas millimeter-precision drawing  
* Multi-page drag-and-drop engine  
* Half-page rendering model  
* URL-based compressed scene storage  

---

# 9. Revision History

| Version | Date           | Notes                                                          |  
| ------- | -------------- | -------------------------------------------------------------- |  
| 1.1     | 2025-12-05     | Initial SVG-based design                                       |  
| 2.0     | 2025-12-07     | Full Canvas engine, artboards, D&D, PNG printing               |  
| **2.1** | **2025-12-08** | **Artboard gutter rules, DPI notes, page number labels added** |

---

# 10. License

This specification and the Liner Tool implementation are protected under the  
**TARP Tools License**.  
Unauthorized reproduction, modification, redistribution, or reverse engineering is prohibited.  

Full policy:
**[https://tools.tarp.tokyo/terms/](https://tools.tarp.tokyo/terms/)**
