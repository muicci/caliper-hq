# Caliper MCP Roadmap (CEO Revision - 2026-02-22)

This roadmap reflects the current strategic direction for Caliper after V2.2 hardening.

## Completion Status Through Phase 10

- [x] **Phase 7: App Shell**
- [x] **Phase 8: Editable Tables**
- [x] **Phase 9: Material Yield Engine**
- [x] **Phase 10: Tooling & OSP Logic**

---

## Phase 11: The Hybrid Vision Pipeline (macOS Native + Claude 3.5 Sonnet)

### Goal

Flawless extraction of messy, scanned engineering drawings by combining local macOS native OCR with BYOK cloud vision.

### Architecture

1. **The Local Layer (Cross-Platform Native OCR via Tauri Rust)**
   - The Local OCR Layer must be cross-platform using Rust `#[cfg]` flags:
   - `#[cfg(target_os = "macos")]`: Utilize Apple's Native Vision API (via Swift/Objective-C bindings).
   - `#[cfg(target_os = "windows")]`: Utilize the native `Windows.Media.Ocr` engine (via the `windows-rs` crate).
   - Both OS-native engines run locally with zero cloud calls, require zero massive Docker containers, and must return an identical JSON array of bounding boxes `[x, y, width, height]` to the Svelte frontend.
2. **The Fallback Layer (High-Res PDF Rendering)**
   - Render the drawing into high-resolution base64 images page-by-page.
   - Preserve enough resolution for title blocks, tolerances, and dense callouts.
3. **The Heavy Brain (Claude 3.5 Sonnet with BYOK)**
   - Send both artifacts together:
     - base64 rendered page image
     - OS-native OCR JSON coordinate map
   - Use the client's API key only (BYOK model).
4. **Strict Output Contract**
   - Claude must return strict quote-draft JSON including exact coordinate references for extracted entities.
   - Required field includes `material_callout_bbox` for UI red-box annotation.
5. **UI Overlay Binding**
   - Workspace consumes returned coordinates and draws red-box overlays on the PDF canvas.
   - If extraction confidence is low, UI routes to Human-in-the-Loop confirmation.

**Magic Prompt**

"Attached is an image of the engineering drawing, plus a JSON map of all text coordinates. Output a strict JSON quote draft with the exact `[x,y]` coordinates of the Material Callout so we can draw a red box around it on the UI."

### Value / Why

- Local OCR coordinates anchor model reasoning to real page geometry.
- Feeding Claude both image + coordinate map reduces hallucinated coordinate outputs.
- BYOK keeps data ownership and API cost control with the client.
- UI annotation creates immediate operator trust by showing exactly where extraction came from.

---

## Phase 12: Historical Quote Embeddings (Local Similarity Engine)

### Goal

Use local historical quote similarity to warn estimators about repeat underestimation risks before quote release.

### Architecture

1. Store finalized quote feature vectors in local SQLite via `sqlite-vec`.
2. Build embeddings from normalized geometry + operations + pricing context.
3. Run nearest-neighbor search during active quote drafting.
4. Surface top similar historical jobs with variance/risk notes in Quote Details.

### Value / Why

- Reuses shop-specific quoting history as practical memory.
- Flags likely cycle-time/tooling misses before they become margin leaks.
- Keeps similarity search local-first and private.

---

## Phase 13: The Universal CSV Exporter (ERP Bridge)

### Goal

Provide ERP-ready CSV exports using configurable field mapping, without building fragile direct ERP API integrations.

### Architecture

1. **Rule:** No direct API integrations to legacy ERPs (JobBOSS/E2/MYOB class systems) until explicitly paid custom scope.
2. Add Settings screen: **ERP Field Mapping**.
3. Map Caliper internal variables to shop-specific ERP column headers.
4. Persist mappings in local SQLite (`erp_config`).
5. On quote approval/export, generate a perfectly formatted `.csv` for bulk ERP import.

### Value / Why

- Eliminates manual re-typing and human transcription errors.
- Works across many ERP systems with one flexible export path.
- Avoids long-term maintenance burden from unpaid API integrations.

---

## Phase 14: The Excel-to-SQLite Ingestor (Internal Dev Tool)

### Goal

Automate client onboarding by converting messy pricing workbooks into clean SQLite seed SQL.

### Architecture

1. Founder exports client workbook tabs to CSV files.
2. Internal script (Node.js or Python, out-of-product) ingests CSVs.
3. Anthropic/Claude-assisted parsing normalizes materials, rates, and units.
4. Script outputs pristine `seed.sql` with `INSERT INTO shop_config` and related seed statements.
5. Founder reviews and approves SQL before execution.

### Value / Why

- Cuts onboarding implementation time dramatically.
- Reduces manual SQL authoring mistakes.
- Creates repeatable migration process for future client rollouts.

---

## Phase 15: Client Memory (The "Shop Quirks" DB)

### Goal

Persist each shop's terminology mappings so the AI stops asking the same normalization questions repeatedly.

### Architecture

1. Add SQLite table `agent_memory` with explicit mappings (`trigger_text` -> `resolved_to`) plus confidence and confirmation metadata.
2. On unknown terms, AI asks once for confirmation in UI.
3. Save confirmed mapping to `agent_memory`.
4. Inject confirmed mappings into future prompts using FTS5 retrieval of relevant memories.
5. Keep memory string-based and explicit for this phase (no vector store expansion).

### Value / Why

- Captures real shop language like `Alum` -> `Aluminum 6061`.
- Improves extraction consistency over time per client.
- Achieves memory gains with low complexity and zero vector bloat.

---

## Phase 16: Rust-Native Agent Tools (The Agent's "Hands")

### Goal

Give the AI agent safe native capabilities through Tauri tools so it can inspect data and propose migrations inside Caliper workflows.

### Architecture

1. Expose `tool_read_excel` backed by `calamine` crate for structural peek/sample reads.
2. Expose `tool_propose_migration` for SQL draft generation.
3. Keep read-only and state-changing tool permissions explicitly separated.
4. **HITL Rule:** Any state-changing tool pauses the ReAct loop and renders Approve/Reject UI before execution.

### Value / Why

- Turns agent from chat advisor into controlled automation engine.
- Preserves safety by design with mandatory approval checkpoints.
- Enables future ingestion and maintenance workflows without shell-level access.

---

## Phase 17: Historical Quote Validation Skill (The Sales Weapon)

### Goal

Prove Caliper quote fidelity during sales demos by comparing generated quotes against a shop's known historical wins.

### Architecture

1. Founder selects 3 historical successful job PDFs.
2. Caliper generates fresh quotes using imported pricing rules.
3. Validation skill compares Caliper outputs against historical Excel quote values.
4. System emits a variance report with line-by-line rationale.
5. Report is packaged as a demo artifact for buyer review.

### Value / Why

- Converts "trust me" sales motion into evidence-based proof.
- Directly addresses buyer skepticism around AI quoting reliability.
- Shortens time-to-confidence for paid implementation decisions.

---

## Phase 18: HITL Approval Hooks (Liability Protection)

### Goal

Guarantee that autonomous agent actions cannot overwrite shop pricing data without explicit human supervision.

### Architecture

1. Classify all SQLite `UPDATE/INSERT` mutations to pricing-critical tables as HITL-required.
2. Before execution, render Svelte approval diff UI (for example: "AI wants to update 5-Axis rate from $120 to $135").
3. Require explicit Approve or Reject decision per mutation batch.
4. Only approved diffs execute; rejected diffs are logged and discarded.
5. Persist approval audit trail for accountability.

### Value / Why

- Prevents silent pricing drift from bad autonomous actions.
- Protects margin, trust, and legal exposure.
- Makes Human-in-the-Loop control a backend-enforced contract, not just a UI suggestion.

---

## Phase 19: The Branded PDF Quote Generator (The Output Artifact)

### Goal

Generate a beautiful, client-ready PDF quote directly from Caliper so the shop owner can email it to their customer immediately.

### Problem Statement

We have perfected data extraction (Phase 11) and internal ERP data export (Phase 13), but we are missing the actual output the shop's customer sees. A $10,000 product must produce a flawless final artifact.

### Architecture

1. **The "Zero-Friction" Branding Agent:** During onboarding, the founder inputs the client's website URL. A background agent scrapes the site, extracts the high-res logo, brand colors (hex codes), and company address, saving them to `shop_config`.
2. **Template Matching:** The shop owner uploads an example of their current quote PDF. A multimodal agent (Claude 3.5 Sonnet) analyzes the layout and generates a matching HTML/Tailwind template.
3. **The Generator:** When the user clicks "Generate Quote", SvelteKit injects the `$draftQuoteState` (line items, materials, OSP, grand total, and terms/conditions) into the branded HTML template.
4. Tauri uses a headless print-to-PDF rust crate (or system webview) to silently render the HTML into a pristine, customer-ready PDF.

### Value / Why

- **The Demo Moment:** "You drag the messy RFQ in, Caliper does the math, you click approve, and BOOM—here is the pristine PDF quote ready to email."
- Justifies the $10,000 setup fee by completely replacing their manual Word/Excel quote creation process with a zero-friction, white-glove branded experience.

---

## Phase 20: The "Air-Gap" Visualizer (Proving Local-First)

### Goal

Visually prove to skeptical, ITAR-compliant machine shops that their proprietary CAD models and drawings are not being stolen by cloud servers.

### Problem Statement

Our entire competitive moat is "Local-First Privacy" (SQLite + Tauri). But non-technical shop owners do not know what that means. If they don't *see* privacy, they won't believe it.

### Architecture

1. Add a persistent, highly visible UI element (e.g., a "Green Shield" or "Network Monitor" widget) in the App Shell footer.
2. The widget actively monitors and displays network traffic originating from the Tauri backend.
3. **The Readout:** It explicitly states:
   - `Local Database: Secured`
   - `Drawings Uploaded: 0`
   - `Active API Connections: 1 (Anthropic BYOK)`
   - `Telemetry/Tracking: Disabled`
4. If the user clicks the shield, it opens a transparent audit log showing exactly which bytes were sent to their own BYOK Anthropic key, and proving that the PDF files themselves never left the hard drive.

### Value / Why

- **The Demo Moment:** "Notice this green shield? Unlike our SaaS competitors, Caliper isn't uploading your ITAR-restricted aerospace drawings to a public AWS server. Every calculation happens right here on your Mac."
- Instantly kills the #1 objection ("I don't trust AI with my data") and secures enterprise compliance.

---

## Phase 21: The Multi-Page Filmstrip Pipeline

### Goal

Handle multi-page PDF packets (POs, T&Cs, Drawings) by providing a premium "Magazine/Filmstrip" thumbnail navigation UI, and batching pages to the Vision AI so it can auto-locate the actual engineering drawing.

### Architecture

1. **The UI Layer (Thumbnail Column):**
   - Do NOT render multiple `pdf.js` canvases (avoids WebGL crashes).
   - Instead, use the Rust `render_pdf_page_png` command (built in Phase 11) at a low resolution (e.g., 50 DPI) to instantly generate lightweight base64 preview images of all pages.
   - Display these previews in a scrollable left-hand or bottom "Filmstrip" column.
   - Clicking a thumbnail updates a Svelte 5 `$state(currentPage)` variable, which commands the single, central `pdf.js` canvas to jump to that page.
2. **The LLM Layer (Batching):**
   - The frontend sends an array of `[Image, OCR]` pairs (for the first 5 pages) to Claude 3.5 Sonnet.
   - **LLM Prompt Update:** "Analyze this document packet. Ignore administrative boilerplate (Purchase Orders, Terms). Find the page containing the actual engineering drawing. Extract the machining data, and include `{\"source_page\": 2}` in your JSON response so the UI knows where the data came from."
3. **UI Overlay Guard:**
   - Red-box annotation overlays (e.g., `material_callout_bbox`) will only render over the central PDF canvas if the user's `$state(currentPage)` strictly matches the AI's extracted `source_page`.

### Value / Why

- Real-world RFQs are almost always multi-page packets.
- A thumbnail sidebar feels like a native, premium desktop application (like Apple Preview or Adobe Acrobat), elevating the perceived value of the $10,000 product.

---

## Phase 22: The Agentic Schema & Math Configurator (Universal Client Configurator)

### Goal

Abstract the `calculator.ts` engine and SQLite schema so that Caliper can be instantly re-configured for any estimating industry (Wood, Metal, Steel) without rewriting the core SvelteKit application.

### Architecture

1. **The "Config-as-Code" Manifest:**
   - Instead of hardcoding terms like "milling_time", create a master `shop_domain.json` file.
   - This file defines the industry nomenclature and rules (e.g., `industry: "joinery"`, `primary_material_unit: "sheets"`, `primary_labor: "cnc_router_time"`).
2. **The LLM Math Compiler (Internal Tool):**
   - When onboarding a new client, feed their existing Excel quoting spreadsheet into an internal Claude Agent.
   - The Agent outputs a custom Javascript/TypeScript `calculator.js` file specific *only* to that client, which is then injected into their local `.dmg` build.
3. **Dynamic Svelte UI Generation:**
   - The Svelte frontend is refactored to read `shop_domain.json`. Instead of a hardcoded table column named "Material ($/kg)", the UI dynamically renders "Material ($/Sheet)" or "Material ($/Ton)" based on the config.
4. **The "Custom Vision Prompt" Injector:**
   - The system prompt sent to Anthropic's Vision API changes based on the industry defined in the config.
   - *If CNC:* "Find the Geometric Tolerances."
   - *If Joinery:* "Find the Cabinet Dimensions and Edge-banding lengths."

### Value / Why

- You build the Core UI and PDF Viewer *once*.
- You use your internal AI Agent to write the custom math and database schema for *every new client*.
- Transforms the business model from a "Software Developer" into an "AI-Powered Implementation Agency," allowing you to sell to a CNC shop on Monday, a Joinery on Wednesday, and a Steel Fabricator on Friday, collecting a $10,000 setup fee each time with minimal manual coding.

---

## Phase 24-27: Commercialization, Security & Scale (The Solopreneur Stack)

## Phase 24: The UI Dictionary (Adaptive Industry Context)
- **Goal:** Replace hardcoded UI labels with dynamic stores based on the client's industry selection.
- **Architecture:** Create a `$dictionary` Svelte store that reacts to `app_config.industry`. If Joinery, `unit`="Board-foot". If CNC, `unit`="Lbs".
- **Bonus:** Implement Light/Dark/System theme toggle using Tailwind `dark:` and Tauri window theme matching.

## Phase 25: The Seat-Limited Licensing Engine (Polar.sh)
- **Goal:** Monetize via device limits and prevent SQLite license tampering.
- **Architecture:** 
  1. Read the OS-level Hardware UUID via Rust.
  2. Send `[LicenseKey, HardwareUUID]` to the Polar.sh API.
  3. Enforce "Seat Limits". Render an upsell UI if a shop tries to install on too many PCs.
  4. Store the validated JWT in `tauri-plugin-stronghold` (Encrypted Enclave), NOT plain-text SQLite.

## Phase 26: The Zero-Touch Updater (GitHub Releases)
- **Goal:** Distribute updates to clients seamlessly without hosting an update server.
- **Architecture:** Configure Tauri's native `updater` to point to a public GitHub repository's Releases endpoint. The app detects new versions, downloads the binary delta, and prompts the user to "Restart & Update". Add a "Community" tab linking to the public repo's Issues page for bug reports.

## Phase 27: AI DevOps (The Solopreneur Pipeline)
- **Goal:** Automate dependency tracking and LLM model updates.
- **Architecture:** A scheduled GitHub Action that utilizes Claude Code (or MCP) to check for upstream SDK/LLM updates. The agent writes the code changes, runs `npm run check` and `cargo check`, and opens a Pull Request. The founder simply reviews the PR and clicks "Merge" to trigger a new release.
