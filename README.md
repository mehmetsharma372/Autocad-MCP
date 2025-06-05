<div align="center">

# AutoCAD MCP Pro

**Production-grade AutoCAD automation for AI agents.**
Live through COM on Windows, or headless through ezdxf anywhere — one typed contract, two engines.

[![CI](https://github.com/U-C4N/Autocad-MCP/actions/workflows/ci.yml/badge.svg)](https://github.com/U-C4N/Autocad-MCP/actions/workflows/ci.yml)
[![PyPI](https://img.shields.io/pypi/v/autocad-mcp-pro?color=1f6feb&label=PyPI&logo=pypi&logoColor=white)](https://pypi.org/project/autocad-mcp-pro/)
[![Downloads](https://img.shields.io/pypi/dm/autocad-mcp-pro?color=8957e5&label=downloads)](https://pypi.org/project/autocad-mcp-pro/)
[![Python 3.11+](https://img.shields.io/badge/python-3.11%2B-1f6feb?logo=python&logoColor=white)](https://github.com/U-C4N/Autocad-MCP/blob/main/pyproject.toml)
[![License: MIT](https://img.shields.io/badge/license-MIT-3fb950)](https://github.com/U-C4N/Autocad-MCP/blob/main/LICENSE)

[Install](#install) · [What's new](#whats-new-in-16) · [Tools](#what-you-get) · [Engines](#the-two-engines) · [Evidence](#evidence) · [Limits](#what-this-release-is-bad-at) · [Config](#configuration) · [Changelog](https://github.com/U-C4N/Autocad-MCP/blob/main/CHANGELOG.md)

<img src="https://raw.githubusercontent.com/U-C4N/Autocad-MCP/main/docs/assets/autocad-mcp-showcase.png" alt="ISO A3 sheet: a module-6, 24-tooth spur gear with a keyed bore, its section A-A, ISO 129 dimensions with an H7 bore fit, and an ISO 7200 title block" width="800">

<sub><b>Not a mockup.</b> Every line on this sheet was drawn by the tools this server exposes — ISO layers, involute gear geometry, DIN 6885 keyway, section A-A, ISO 129 dimensions, an ISO 286 <code>H7</code> bore fit, ISO 7200 title block — then rendered headlessly by <code>view_screenshot</code>. <code>drawing_critique</code> returns <b>0 issues</b> on it. Rebuild it with <code>python scripts/render_readme_showcase.py</code>.</sub>

</div>

> **v1.6 release snapshot:** 247 tools · 8 resources · 5 prompt templates · 4992 collected tests.
> 247 is the **registered** count; a default install advertises 242 over `tools/list`,
> because `ENABLE_3D` is unset. `system_about` is the runtime authority.

## What's new in 1.6

Five tracks of one [roadmap](https://github.com/U-C4N/Autocad-MCP/blob/main/docs/superpowers/specs/2026-09-15-v1.6-roadmap-design.md), 154 → 247 registered tools. Each track has its own design spec, correctness checks that can fail, and a live AutoCAD 2026 run of its COM paths recorded in the [changelog](https://github.com/U-C4N/Autocad-MCP/blob/main/CHANGELOG.md).

| Track | What it adds | Pack |
|---|---|---|
| **P&ID — drafter and reader** | 43 ISO 10628-2 / ISA-5.1 symbols as real blocks with ports and `TAG` attributes, port-to-port lines with ISA-5.1 classes and line numbers, `pid_graph` reading any P&ID back with a confidence per node, instrument index, line list, equipment list, `pid_from_spec` for a whole sheet in one transaction | `pid` |
| **The AutoCAD environment** | ISO-25 / ANSI dimension styles, text and leader styles, `drawing_apply_standard`, ISO 216 / ANSI page setups checked against the plotted PDF's own `/MediaBox`, five bundled templates, several open documents headlessly, portable layer states, named views, UCS, a 90-entry system-variable catalogue; on a live seat, preferences, launch and operator prompts | `core`, `settings` |
| **Mechanical parts and the sheet** | one part model and one view engine (front, side, top, section and detail views with hidden lines, cut faces and ISO 129 dimensions), ISO fasteners and bearings as blocks with attributes, ISO 21920-1 surface texture, ISO 2553 welds, ISO 5457 frames A4–A0, ISO 7200 title blocks, an ISO 7573 parts list read off the drawing with linked ISO 6433 balloons, xrefs, images, real `.dwg` | `mech`, `core` |
| **Architecture** | walls with hosted doors and windows: one engine resolves L / T / X junctions and cuts the openings. Room areas are measured from the faces the walls enclose, and rooms are read back from any plan. Door / window / room schedules are real tables; labels in English or Turkish | `arch` |
| **Understanding and takeoffs** | `drawing_understand` reads a drawing somebody else made in one call. Also `drawing_scale_check`, `drawing_diff` and `drawing_topology_check`. `pipe_takeoff` / `cable_takeoff` take the topology from the P&ID and the lengths from the layout into a Turkish, English or Russian workbook | `core`, `plant` |

`TOOL_PACKS` keeps a client from paying for the tracks it does not use ([idle cost below](#why-this-exists)).

<div align="center">

<img src="https://raw.githubusercontent.com/U-C4N/Autocad-MCP/main/docs/assets/mech-showcase.png" alt="ISO A3 flange-coupling sheet: a hub with a full section and chain dimensions, a flange with its bolt-circle end view, two ISO 4014 bolts with ISO 4032 nuts, a parts list with linked balloons, an ISO 5457 frame with zones and an ISO 7200 title block" width="800">

<sub><b>A mechanical sheet, gated.</b> The <code>mech_assembly</code> benchmark sheet: a hub drawn from its part model with a full section and chain dimensions, a flange with its bolt-circle end view, two ISO 4014 M12×60 bolts and ISO 4032 nuts inserted as blocks, the ISO 7573 parts list <i>read off the drawing</i>, balloons linked to its rows, an ISO 5457 frame and an ISO 7200 title block. <code>drawing_critique(focus=None)</code> returns <b>0 issues</b> and the finalize score is <b>100</b>. Rebuild it with <code>python scripts/render_readme_mech.py</code>.</sub>

<img src="https://raw.githubusercontent.com/U-C4N/Autocad-MCP/main/docs/assets/arch-showcase.png" alt="Two-room floor plan: hatched brick walls with an aerated-concrete partition, two doors, two windows, a straight stair, room labels with measured areas, exterior dimension chains, and door, window and room schedules" width="800">

<sub><b>A floor plan whose areas nobody typed.</b> <code>arch_plan_from_spec</code>'s example, drawn in one transaction: a 250 mm brick ring split by a 100 mm AAC wall, two doors, two windows, a straight stair and exterior dimension chains at 1:50. The rooms measure 27.74 and 21.99 m², read from the faces the walls enclose, and the door, window and room schedules are read off the drawing. <code>drawing_critique(focus=None)</code> returns <b>0 issues</b>. Rebuild it with <code>python scripts/render_readme_arch.py</code>.</sub>

<img src="https://raw.githubusercontent.com/U-C4N/Autocad-MCP/main/docs/assets/pid-showcase.png" alt="P&ID sheet: a centrifugal pump, a diaphragm-actuated globe control valve, a vertical vessel with two nozzles, a flow controller bubble and an off-page connector, joined by numbered process lines and an electric signal line" width="800">

<sub><b>The same server as a P&ID drafter.</b> <code>pid_from_spec</code> placed five catalogue symbols as real blocks with <code>TAG</code> attributes and named ports and routed four port-to-port lines with ISA-5.1 classes and line numbers, all in one transaction. Then <code>pid_graph</code> read the sheet back: 5 nodes, 4 edges, 0 dangling ends, <code>confidence_min</code> 1.0. Rebuild it with <code>python scripts/render_readme_pid.py</code>.</sub>

<img src="https://raw.githubusercontent.com/U-C4N/Autocad-MCP/main/docs/assets/pid-catalog.png" alt="Contact sheet of the 43 P&ID catalogue symbols: valve bodies with actuators, pumps and compressors, heat exchangers, vessels, instrument bubbles, line markers and connectors" width="800">

<sub><b>The catalogue, authored in code.</b> 43 ISO 10628-2 / ISA-5.1 symbols: 11 valve bodies × 6 actuators, 6 rotating machines, 4 heat exchangers, 6 parametric vessels, 7 inline items, the instrument bubble in 4 types × 5 locations, 5 line markers and 3 connectors. Every one was placed through <code>pid_symbol_insert</code>'s own path. Rebuild it with <code>python scripts/render_pid_catalog.py</code>.</sub>

</div>

## Why this exists

**A big MCP server is expensive to be connected to.** The full catalog costs a client **83,921 tokens** before it has asked for anything. Discovery mode replaces it with two tools and costs **356**.

**A drafter searches for `FILLET`, not `entity_fillet`.** Before the corpus, those command names appeared in no tool name or description. That was `df = 0` against a stock index: the tools were not ranked badly, they were *absent*. The fix was data: an authored corpus of **206 AutoCAD command names and 1439 synonym phrases** covering all 247 tools. A test refuses to let a tool exist without one.

| Advertised surface | Tools seen | Idle cost |
|---|---:|---:|
| `TOOL_PROFILE=full` (default) | 242 | 83,921 tokens |
| `TOOL_PACKS=core,settings` (full profile) | 205 | 63,890 tokens |
| `TOOL_PACKS=core,mech` (full profile) | 197 | 64,836 tokens |
| `TOOL_PACKS=core,arch` (full profile) | 195 | 63,549 tokens |
| `TOOL_PACKS=core,pid` (full profile) | 192 | 60,844 tokens |
| `TOOL_PACKS=core,plant` (full profile) | 185 | 59,290 tokens |
| `TOOL_PACKS=core` (full profile) | 183 | 57,122 tokens |
| `TOOL_PROFILE=lean` | 65 | 22,661 tokens |
| `DISCOVERY_MODE=search` | 2 | **356 tokens** |

> [!NOTE]
> These are offline ratio estimates, not tokenizer counts, and they are the *uncached* cost: prompt caching amortises the idle term. `benchmarks/token_suite.py --tokenizer anthropic` counts for real.

## Install

```bash
pip install autocad-mcp-pro     # or: uvx autocad-mcp-pro
autocad-mcp                     # stdio MCP server, backend auto-selected
```

```bash
AUTOCAD_MCP_BACKEND=ezdxf autocad-mcp    # portable DXF engine, no AutoCAD needed
AUTOCAD_MCP_BACKEND=com   autocad-mcp    # live AutoCAD (needs the [com] extra)
```

| Extra | Pulls in | For |
|---|---|---|
| *(none)* | `fastmcp`, `ezdxf`, `pydantic` | Headless DXF on any OS — **no rendering** |
| `[com]` | `pywin32`, `Pillow` | Live AutoCAD control + window capture |
| `[pdf]` | `matplotlib` | PDF export and headless PNG, any platform |
| `[office]` | `openpyxl` | XLSX workbooks (takeoffs, `data_extract`); CSV is always written |
| `[full]` | everything above | Development and CI |

> [!NOTE]
> The bare install **cannot draw pixels** — `ezdxf.addons.drawing` imports Pillow unconditionally, so every render path needs it. Add `[pdf]` for images on Linux or macOS.

<details>
<summary><b>Wire it to an MCP client</b></summary>

```json
{
  "mcpServers": {
    "autocad": {
      "command": "autocad-mcp",
      "env": {
        "AUTOCAD_MCP_BACKEND": "auto",
        "ALLOWED_PATHS": "C:\\Users\\you\\Documents\\AutoCAD",
        "TOOL_PROFILE": "full",
        "TOOL_PACKS": "all",
        "DISCOVERY_MODE": "off"
      }
    }
  }
}
```

It works with Claude Desktop, Cursor, or any stdio MCP host. For HTTP, run `autocad-mcp --transport http --port 8000`. It binds loopback only, unless remote HTTP is explicitly enabled **and** a bearer token is set.

</details>

## What you get

| Area | What it does |
|---|---|
| Drawing lifecycle | create, open, save, export DXF/PDF, audit *(repairs)*, purge, undo/redo |
| Geometry | lines, arcs, polylines, splines, hatches, trim/extend/fillet/chamfer, handle-preserving edits |
| Annotation | ISO 129 toleranced dimensions, ISO 286 fits (`fit="H7"`, interference shafts r/s/t/u included), TABLE, MLEADER, GD&T frames and datums (ISO 1101) |
| Engineering generators | involute gears (front + section A-A), DIN 6885 keyed bores, ISO A3 title block |
| Mechanical parts | one part model (segments or outline + typed features) and one view engine: front / side / top / section / detail with hidden lines, ISO 128-50 cut faces and ISO 129 dimensions. ISO 4014/4017/4032/7089/4762 fasteners and ISO 15 bearings insert as real blocks with attributes. DIN 471/472, DIN 509, DIN 332 and ISO 3601-2 features have their drawing code, but *their tables ship empty in 1.6* (see [limits](#what-this-release-is-bad-at)). Also ISO 21920-1 surface texture, ISO 2553 welds and ISO 128-40 section lines |
| Sheet & delivery | ISO 5457 frames A4-A0 with zones and trim marks, ISO 7200 title blocks for every size, revision blocks with clouds, ISO 7573 parts lists with ISO 6433 balloons linked by XDATA, CSV/XLSX extraction, xrefs and images on both engines, real `.dwg` on a live seat |
| Architecture | one plan model (walls with hosted doors and windows) and one engine that resolves L / T / X junctions, cuts openings and draws poché by material. Room areas are measured from the faces the walls enclose, never typed, and rooms are read back from any plan with a confidence. Door / window / room schedules are real TABLEs. Also a furniture and sanitary catalogue at nominal sizes, a structural grid, north arrow, section / level / elevation marks, exterior dimension chains, and English or Turkish labels (`lang="tr"`, decimal comma) |
| Understanding & takeoffs | `drawing_understand` reads a drawing somebody else made in one call: declared vs inferred units, robust extents and the outlier behind them, plan copies, each layer's discipline and service in five languages, tags, rooms. `drawing_scale_check` says whether a P&ID is to scale against its layout. `pipe_takeoff` / `cable_takeoff` take the topology from the P&ID and the lengths from the layout (rectilinear MST, statuses A/B/C, power never invented) into a Turkish / English / Russian workbook. `drawing_diff` compares revisions; `drawing_topology_check` finds dangling ends, near misses and crossings |
| P&ID | catalogue blocks with ports and tags (ISO 10628-2 / ISA-5.1), port-to-port lines with ISA-5.1 classes and line numbers, `pid_graph` reads any P&ID back with confidence, instrument index / line list / equipment list, `pid_from_spec` one-call sheets |
| Styles & standards | ISO-25 / ANSI dimension styles, ISOCP / ROMANS text styles, ISO / ANSI leader styles from authored presets; `drawing_apply_standard("iso")` sets all of it plus units and the `mech` layers in one call; `changed` reports only what moved |
| Discovery | `search_tools` ranked over an AutoCAD command and synonym corpus — `FILLET`, `BPOLY`, `QSELECT`, `WBLOCK`, `OVERKILL`, `CHSPACE` each rank **#1** of the 242-tool advertised catalog |
| Batching | `cad_batch` runs a step list in one round trip; `fields=` projects 11 result-heavy tools |
| Paper space | tab lifecycle, viewports, `entity_change_space` (CHSPACE), `drawing_export_pdf(layout=…)` |
| Page setup & templates | ISO 216 / ANSI Y14.1 sheets, ctb catalog, `page_setup_apply` on both engines, and `batch_plot` with every sheet size read back from its PDF's `/MediaBox`. Five bundled templates, built by the server's own tools and pinned reproducible: `drawing_new(template="iso_a3_mech")`, `iso_a1_arch`, `iso_a3_pid`, `ansi_b_mech`, `ansi_d_arch`. Save any drawing as a template (`.dwt` on live AutoCAD; `dwt_write` refused headlessly) |
| Environment | several open documents headlessly, portable layer states (`ACADMCP_LAYERSTATES` XRECORDs — in the file, not in AutoCAD's Layer States Manager), named views, UCS *(tool coordinates stay WCS)*, a system-variable catalog with ranges, document properties. On a live seat: launch/attach, preferences, and an operator prompt / pick / select, where ESC is `cancelled`, not an error |
| Selection | window vs crossing stated back to the caller; a polygon tested against its own shape, not its bounding box |
| Boundaries | `boundary_trace` (BOUNDARY/BPOLY) chains loose edges into one closed polyline, arcs kept as bulges *(headless)* |
| Measurement | `analysis_measure_entity` measures what is *in* the drawing, by handle |
| Hatch depth | gradients, in-place edits, typed edge boundaries *(headless)*, island styles |
| Annotation objects | WIPEOUT, REVCLOUD, MTEXT background masks *(headless)*, text find/replace |
| 3D solids | `solid_box/cylinder/extrude/revolve/boolean` on live AutoCAD (`ENABLE_3D=true`) |
| Quality loop | `drawing_preflight` → `drawing_plan` → `drawing_critique` → `drawing_refine` → `drawing_finalize` (0–100 score); 18 domain critique focuses (P&ID 6, mechanical 6, architecture 3, topology 3), each silent on a drawing outside its domain |
| Delivery | `drawing_deliver`: DXF/PDF/PNG + SHA-256 manifest + reopen-parity checks |

<sub>247 tools in 28 groups. <code>TOOL_PACKS=core</code> hides these from a client that needs none of them: the nine <code>pid_*</code> tools, the 22 environment tools, the 14 mechanical tools, the 12 architectural tools and the two plant takeoffs. <code>TOOL_PACKS=core,mech</code> keeps the mechanical ones, <code>TOOL_PACKS=core,arch</code> the architectural ones, and <code>TOOL_PACKS=core,plant</code> the takeoffs. There are also 8 resources that cost nothing in the tool budget (<code>autocad://drawing/info</code>, <code>layers</code>, <code>blocks</code>, <code>entities/stats</code>, <code>entities/{layer_name}</code>, <code>system/status</code>, <code>pid/symbols</code>, <code>standards/isa51</code>) and 5 prompt templates.</sub>

**Two rules worth knowing.**

- Every coordinate in and out of a tool is WCS on both engines. The one exception is TEXT `rotation`, which stays in the entity frame: a mirrored TEXT is mirror-imaged, and no scalar angle expresses that.
- Never read vertices back and shoelace them. That silently loses **28.2%** of the area on a semicircular edge. `analysis_measure_entity(handle)` reads the real geometry and states its own accuracy.

## The two engines

One contract across 23 modules in `backends/contracts/`. `@capability(key, reason=…)` supplies a default that *refuses*, and a test holds both backends' capability key sets equal. There are **41 capability keys**. Read `system_capabilities` at runtime rather than trusting the table: several depend on what the machine has installed (matplotlib, openpyxl, the ODA File Converter).

| Capability | COM (live AutoCAD) | ezdxf (headless) |
|---|:---:|:---:|
| Live document control | ✅ | — |
| Cross-platform, no AutoCAD | — | ✅ |
| Transactions and rollback | ✅ | ✅ |
| Paper-space layouts + viewports | ✅ | ✅ |
| Viewport model-content rendering | ✅ | ✅ ᵐ *(no borders)* |
| Selection window / crossing / polygon | ✅ | ✅ |
| Entity area by handle | ActiveX `.Area` | Analytic, bulges included |
| HATCH filled area (islands subtracted) | AutoCAD's own number | Loops walked, `hatch_style` reported |
| REGION / 3DSOLID area | ✅ | — *ACIS is opaque to ezdxf* |
| 3D solids | ✅ *with `ENABLE_3D=true`* | — |
| DWG write | ✅ `SaveAs` | via the ODA File Converter, if installed |
| `.dwt` templates | ✅ | DXF only — `dwt_write` refuses |
| Reading a foreign drawing (`drawing_understand`, takeoffs) | ✅ one `Document.Export` snapshot; the document is never renamed | ✅ |
| XLSX workbooks | ✅ ᵒ | ✅ ᵒ |
| Preferences · launch · operator prompts | ✅ | — *refused by name* |
| WIPEOUT · MTEXT background colour | — *verified absent, AutoCAD 2026* | ✅ |
| REVCLOUD · BPOLY · typed hatch edges | — *no ActiveX member* | ✅ |
| CHSPACE | — *unverified on a live seat* | ✅ ᶜ |
| Undo history | ✅ | opt-in — `EZDXF_UNDO_DEPTH` |
| TABLE and MLEADER | Native | Portable composite |
| Screenshots and PDF | Window capture | Matplotlib ᵐ |

<sub>ᵐ Needs matplotlib (<code>[pdf]</code>/<code>[full]</code>). Without it, <code>png</code>, <code>pdf</code>, <code>viewport_render</code> and <code>handle_overlay</code> report unsupported headlessly.<br>
ᵒ Needs openpyxl (<code>[office]</code>/<code>[full]</code>). Without it, XLSX is refused with <code>xlsx_write</code> and CSV is still written.<br>
ᶜ Headless CHSPACE has four restrictions, named in its capability reason: top-view untwisted viewports only; dimensions refused unless frozen; ACIS, proxy and table entities refused; viewport clipping reported rather than applied.</sub>

> [!IMPORTANT]
> Every COM path added in v1.6 was executed against a **live AutoCAD 2026**, one smoke script per track (`scripts/smoke_*_com.py`), each run recorded in the changelog. Those runs found defects no headless test could. ActiveX refuses `entity.Layer` on a missing layer, where ezdxf quietly creates it, so `mech_part_draw` died aft