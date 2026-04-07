# IPv4 Network Tool

A fully self-contained, offline-capable IPv4 subnet calculator built as a single HTML file. No dependencies, no server, no installation — just open the file in any browser.

**Live version:** [https://andrew-to-code.github.io/subnet-calc/](https://andrew-to-code.github.io/subnet-calc/)

---

## Features

- **CIDR subnet input** — enter any network in `10.0.0.0/24` format and click Load
- **Divide & Join** — recursively split any subnet into two equal halves; join them back with one click. Cascading vertical join buttons span the rows they would merge.
- **Description field** — add a free-text label to any subnet row. Rows without a description are shown in grey; rows with one are shaded by depth.
- **Sheet description** — a top-level description field to note what the whole plan is for
- **Row highlighting** — click any non-input cell to toggle a yellow highlight for callouts or attention
- **Column visibility toggles** — hide/show Netmask, Range, Useable IPs, Hosts, and Description independently
- **Export CSV** — exports all subnets with full network details (subnet, mask, range, useable IPs, host count, description)
- **Import CSV** — load a subnet plan from a CSV file; download a template to get started
- **Permalink** — every change updates the URL with a compressed, shareable link that fully restores the exact state (tree structure, descriptions, highlights, sheet description)
- **Saved states** — every Divide/Join action saves a snapshot with a timestamp; click ↩ Restore to undo any change
- **Reference panel** — built-in subnet mask table (/32–/0) and a Reserved IPv4 Ranges reference (RFC 1918, CGNAT, Link-Local, Loopback, Documentation, Experimental, Broadcast)
- **Dark mode** — toggle between light and dark themes; preference saved across sessions

---

## Usage

1. Download `subnet-calc.html`
2. Open it in any modern browser
3. Enter a network (e.g. `192.168.1.0/24`) and click **▶ Load**
4. Use **Divide** to split subnets and the vertical **Join** buttons to merge them back
5. Add descriptions to each subnet row
6. Use **⬇ Export CSV** to save your plan or **⬆ Import CSV** to load one

No internet connection required after download.

---

## Import CSV Format

The importer accepts a CSV with the following columns (header names are flexible):

| Column | Required | Aliases accepted |
|---|---|---|
| Subnet Address | ✅ Yes | `Subnet`, `CIDR`, `Network` |
| Description | No | `Desc` |

The first data row must be the supernet (e.g. `10.0.0.0/16`). Subsequent rows are subnets within it. Download the template from the Import modal for an example.

---

## Technical Notes

- Single HTML file — zero external dependencies, zero network calls
- All state is encoded in the URL using LZ-string compression + bit-packed tree serialisation
- Permalink URLs are fully self-contained and can be shared or bookmarked
- Built with vanilla JavaScript (`'use strict'`, no frameworks)

---

## Built With

This tool was built with the assistance of **[Claude](https://claude.ai)** (Anthropic). The following prompt can be used to recreate the file from scratch:

---

<details>
<summary><strong>📋 Recreate this tool — prompt for Claude</strong></summary>

```
Build a fully self-contained single offline HTML file called subnet-calc.html. Zero external
dependencies, no CDN, no network calls, 100% client-side. Use 'use strict' JS with no frameworks.

---

PAGE STRUCTURE

- Browser tab title and h1: "IPv4 Network Tool" — h1 at 26px, dark blue #1a3a5c
- The h1 and a 🌙 Dark / ☀️ Light theme toggle button sit on the same flex row
- A blue horizontal rule (border-top: 2px solid #4a7ab5) below the h1 row
- Footer at the very bottom (left-aligned, 13px, #778): "Created by andrew-to-code 2026"
  linking to https://github.com/andrew-to-code/subnet-calc, with a second line linking to
  https://andrew-to-code.github.io/subnet-calc/

---

TOOLBAR (.inst-topbar)

Styled box (#dde8f8 background, #a8c0e8 border, border-radius: 4px), flex row:
- Label "Network" + text input accepting CIDR format e.g. 10.0.0.0/24. Enter triggers Load.
- ▶ Load button (blue), ✕ Clear button (red)
- Inline error message span
- Columns: checkboxes (each hides/shows matching col-* class cells and headers):
  Netmask, Range, Useable IPs, Hosts, Description
- Far right (margin-left: auto): ⬆ Import CSV (purple #7b5ea7) and ⬇ Export CSV (green #2e8b57)

---

SHEET DESCRIPTION BAR

Between the toolbar and table: a flex bar (same #dde8f8 box styling) with a bold "Description"
label and a full-width transparent text input. The user types a free-form note about what this
network plan is for. Saved to permalink and snapshots.

---

SUBNET TABLE

Columns in order:
1. Subnet address — narrow (width:1px, nowrap), shows IP/bits in bold monospace
2. Netmask (col-netmask)
3. Range of addresses (col-range)
4. Useable IPs (col-useable)
5. Hosts (col-hosts, right-aligned)
6. Description (col-desc, min-width 280px) — editable text input, transparent/borderless until
   hover/focus. Enter moves to next row's description.
7. Unlabelled narrow column showing the CIDR prefix (/24) for each row in monospace gray
8. Divide — compact blue button; disabled at /32
9. Cascading Join columns — one vertical rotated column per level of division depth. Each shows
   a /{bits} label. Active ones are blue buttons that collapse all descendants back to that parent.
   Inactive cells show a muted gray label. Buttons use position:absolute; inset:0.

Row behavior:
- Only leaf nodes shown
- Compact rows: padding 1px 6px, line-height 1.3, fixed height 22px
- Depth-based blue shading: d-0 (#bfd5f5) through d-5 (#f8fbff)
- Rows with no description are grey (#d8d8d8 !important). Typing instantly removes grey.
- Clicking a non-input cell toggles yellow highlight (#ffff88). Clicking anywhere inside a
  Description cell must NOT trigger highlighting — check if the clicked <td> contains a
  .desc-input before toggling.
- Yellow overrides grey. Clicking again removes yellow; grey returns if still no description.

---

EXPORT CSV

- First row: root supernet with Description = "Supernet"
- Followed by all leaf rows
- Columns: Subnet Address, Netmask, Range Start, Range End, Useable Start, Useable End,
  Hosts, Description
- Filename: {CIDR}.csv (slashes replaced with underscores)

---

IMPORT CSV — MODAL

Clicking ⬆ Import CSV opens a fixed modal overlay. Contains:
- Instructions: required column Subnet Address, optional column Description
- ⬇ Download Template button — downloads subnet_import_template.csv with 4 example rows
- 📁 Choose CSV File… button + hidden file input
- Status message, Close button
- Accepts header aliases: Subnet Address / Subnet / CIDR / Network and Description / Desc
- On import: parses CSV, builds tree by dividing from supernet down to each subnet,
  populates descriptions
- Close on button click or clicking outside modal

---

PERMALINK & SAVED STATES BOX

Rounded box matching toolbar styling, below the table (separated by HR):
- Header row: "Permalink & Saved States" label + Hide/Show toggle
- Two-column layout: left 40% = permalink URL (word-break: break-all), right 60% = saved states
- Every Divide/Join saves a snapshot: full tree + descriptions + highlights + sheet description
- Saved states list: #N  label  →  timestamp  [↩ Restore], timestamp format MM/DD/YY HH:MM:SS AM/PM
- ↩ Restore fully restores that exact state
- URL format: file.html?i1n=10.0.0.0/16&i1d=<compressed>&i1desc=<base64>&i1sd=<encoded>&i1hi=<keys>

---

URL PERMALINK COMPRESSION

Both combined, fully client-side:
- LZ-string (~200 lines inline, public domain) — LZ77 + base64url
- Bit-packing: D/L tree string → bits → packed bytes → base36-length-prefixed base64url →
  LZ-compressed
- packTree(dlStr) → compressed string; unpackTree(packed) → D/L string
- Graceful fallback: if decompression fails, treat as raw D/L string

---

REFERENCE SECTION

Rounded box below state bar, titled "Reference" with Hide/Show toggle.
Two-column flex layout inside:

Left column — "Subnet Mask" table (generated by JS, /32 down to /0):
- Columns: CIDR, Subnet Mask, Wildcard Mask, Total Addresses, Useable Hosts
- Rows /32, /24, /16, /8, /0 highlighted bold (#dce8f8 background)
- Compact rows, 11px font, blue header

Right column — "Reserved IPv4 Ranges" (static HTML):
- Private (RFC 1918) with mini-table: 10/8, 172.16/12, 192.168/16
- Carrier-Grade NAT (RFC 6598): 100.64.0.0/10
- Link-Local (RFC 3927): 169.254.0.0/16
- Loopback (RFC 1122): 127.0.0.0/8
- Documentation (RFC 5737) with mini-table: 192.0.2/24, 198.51.100/24, 203.0.113/24
- Experimental (RFC 1112): 240.0.0.0/4
- Broadcast: 255.255.255.255/32
- Footer note about ISP blocking and ARIN
- RFC badges in muted #778 color; body text #334; mini-tables use same blue header as main table

---

DARK MODE

Toggle button (pill style, border-radius: 20px) next to h1. On click toggles body.dark class
and saves preference to localStorage key sn_theme. Key dark-mode overrides:
- Body: #16191f background, #dde4f2 text
- Panels/toolbars: #1e2533 background, #2c3f58 borders
- Table header: #1c3a60; row depths: subtle blue-grey gradient from #18202e to #24354e
- No-desc rows: #1c1e22; highlighted rows: #3a3600 with #f5f200 text
- Subnet addresses: #88c0e8; permalink: #6ab0e8
- RFC content text: #ccd8ec; RFC badges: #7aa8c8
- All inputs, buttons, borders, modal — fully themed

---

JS ARCHITECTURE

- Shared pure functions at top level: IP math, tree helpers, tree serialisation
- createInstance(containerEl, instIndex) factory — all state scoped inside
  (rootNet, rootBits, tree, descriptions Map, highlighted Set, snapshots Array)
- Public API: getState(), restoreState({cidr, division, descs, hi, sheetDesc}), setPermalink(url)
- Boot: document.querySelectorAll('.sn-instance').forEach((el,i) => createInstance(el, i+1))
- Global buildFullUrl() encodes all instances; updateAllPermalinks() calls history.replaceState
- restoreFromUrl() IIFE reads URL params on load
- try/catch on all history.replaceState calls
```

</details>

---

## License

MIT
