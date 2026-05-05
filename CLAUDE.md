# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**CertTrack IT** is a certification management system for IT consulting firms, built as a single self-contained HTML file (`certtrack-it-full.html`) intended for deployment as a SharePoint Online Web Part or SPFx component.

There is no build system, no package manager, and no external JS dependencies — the app runs directly in a browser. The second file, `certtrack-implementacion.html`, is a standalone implementation guide (documentation only, no logic).

## Running the App

Open `certtrack-it-full.html` directly in a browser. No server required.

- **Demo mode** is active by default (`CFG.mode = 'demo'`). All data is hardcoded in JS arrays at the top of the file.
- Switch demo user roles via the UI to test admin vs. employee views.
- To connect to real SharePoint, set `CFG.mode = 'live'` and fill in `CFG.spSite`, `CFG.spUser`, `CFG.spRole` from the SharePoint context object (`_spPageContextInfo`).

## Architecture

### Single-File Structure

All CSS, HTML, and JavaScript live inside `certtrack-it-full.html`. The layout is:

1. `<style>` — all CSS, using CSS custom properties for theming
2. `<body>` — static shell (header, sidebar, main content area, modals)
3. `<script>` — all application logic

### JavaScript Architecture

**Master data** (`VENDORS`, `MASTER_CERTS`, `MASTER_ENTITIES`, `COLABORADORES`, `CERTS`, `ROADMAP`, `BUDGET_CFG`) is defined at the top of the script as plain arrays/objects. In `live` mode these would be replaced by SharePoint REST API calls.

**Global state** lives in the `CFG` object:
- `CFG.mode` — `'demo'` | `'live'`
- `CFG.user` / `CFG.role` — current user identity and role (`'admin'` | `'empleado'`)
- `CFG.spSite`, `CFG.spUser`, `CFG.spRole` — SharePoint context values

**Rendering pattern**: Each page is a function (`renderHome()`, `renderMisCerts()`, `renderAlerts()`, `renderRoadmap()`, `renderAllTable()`, `renderEmpleados()`, `renderSkills()`, `renderBudget()`, `renderReporte()`, `renderMaestros()`) that generates HTML strings and injects them via `innerHTML`. Navigation is handled by `goPage(id)` which shows/hides page divs and calls the relevant render function.

**Role gating**: `vis(arr)` filters data arrays — admins see all records, employees only see their own. `goPage()` also blocks navigation to admin pages for non-admins.

**Data mutations**: `saveCert()`, `delCert()`, `saveColab()`, `saveRoadmap()`, `addMasterCert()`, `delMasterCert()`, `addMasterEnt()`, `delMasterEnt()` modify the in-memory arrays and re-render. In `live` mode, these would call SharePoint REST endpoints instead.

**Utilities**:
- `days(dateStr)` — days until expiration from a `yyyy-MM-dd` string
- `calcSt(dateStr)` — returns `'Vigente'` / `'Por Vencer'` / `'Vencida'` based on days remaining (threshold: 30 days)
- `fd(dateStr)` — formats `yyyy-MM-dd` → `dd/MM/yyyy`
- `toast(msg, type)` — shows a transient notification (`'ok'` | `'warn'` | `'err'`)

### SharePoint Integration

The app is designed for these SharePoint lists:

| List | Purpose |
|---|---|
| `Certificaciones` | Main certification records (item-level permissions) |
| `Colaboradores` | Employee master (employees can only see own record) |
| `MaestroCertificaciones` | Certification catalog |
| `MaestroEntidades` | Certification authorities |
| `Roadmap` | Planned certifications |
| `DocumentosCerts` | Document library for badges/PDFs |

Azure AD groups expected: `CertTrack_Admins` (Full Control), `CertTrack_Empleados` (Contribute + Read).

Three Power Automate flows handle automated email alerts (30-day expiration warning, expiration notice, weekly admin summary). Full schemas and flow definitions are in `certtrack-implementacion.html`.

### CSS Conventions

- All theming via CSS variables defined on `:root` (dark theme with blues, greens, and reds).
- Vendor-specific colors use classes like `.v-gcp`, `.v-azure`, `.v-aws`.
- Status badges use `.t-active`, `.t-warn`, `.t-danger`, `.t-process`.
- Page transitions use the `.up` keyframe animation.

## Design Decisions

This section documents architectural and UI decisions to ensure consistency across future work and provide context for why certain patterns were chosen.

### Format

When documenting a design decision:

```markdown
### [Decision Title] (YYYY-MM-DD)
**Context:** What problem or requirement led to this decision?
**Decision:** What was chosen and why?
**Alternatives considered:** What other options were evaluated?
**Impact:** How does this affect the codebase or user experience?
```

### Current Decisions

(Design decisions will be logged here as they are made)
