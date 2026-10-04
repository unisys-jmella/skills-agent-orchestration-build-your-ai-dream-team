# Project Pulse implementation plan

## Summary

Build a lightweight, static Project Pulse dashboard that helps Mona's contributors
quickly see active projects, their owners, current status, recent activity, and
priority or risk. The dashboard will use a polished, responsive card layout and
run locally from a VS Code launch configuration. Keep the implementation small:
no framework, build step, or third-party runtime dependency is required.

## Implementation steps

### 1. Agree on the integration contract

- **Orchestrator** coordinates the work and confirms the shared contracts before
  parallel implementation begins.
- **Designer** defines the visual hierarchy, responsive behavior, accessibility
  expectations, and the card markup/CSS hooks. Use `.dashboard` for the main
  content and `.project-card` for each project.
- **Coder** follows the top-level `projects` array in `app/project-data.json`;
  each project has `name`, `owner`, `status`, `recentActivity`, `priority`,
  `health`, `progress`, and `milestones`. See
  `docs/project-pulse-design.md` for the field shapes and display behavior.
- Keep file ownership separate: Designer owns `app/styles.css`; Coder owns
  `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.

### 2. Implement the dashboard assets

- **Designer — `app/styles.css`:** create a polished, readable, responsive
  dashboard style with project cards, status badges, clear priority treatment,
  adequate contrast and spacing, rounded corners, and subtle shadows. Ensure the
  layout works on narrow and wide screens and honors reduced-motion preferences
  if animations are used.
- **Coder — `app/project-data.json`:** add several representative projects in a
  top-level `projects` array. Include all five required fields for every
  project, plus the health, progress, and milestone fields specified in
  `docs/project-pulse-design.md`. Use concise, contributor-friendly values.
- **Coder — `app/index.html`:** use the exact page title `Project Pulse`,
  semantic and accessible HTML, and reference `styles.css` and
  `project-data.json`. Load the project data and render a visible
  `.project-card` for every project, showing its name, health, owner, progress,
  recent activity, next milestone, status, and priority. Follow the data and
  presentation contract in `docs/project-pulse-design.md`. Include a clear
  loading state and a useful error message if the data cannot be loaded or is
  invalid.
- **Coder — `.vscode/launch.json`:** add strict JSON with a configuration named
  **Run Project Pulse Dashboard**. Run `python3 -m http.server 5500` with
  `cwd` set to `${workspaceFolder}/app`, and use `serverReadyAction` to open
  `http://localhost:%s/index.html`, not the app directory listing.

### 3. Integrate and validate

- **Orchestrator** checks that the HTML classes and structure match the
  Designer's CSS and that the data fields match the Coder's rendering logic.
- Resolve any mismatch within the owning files, then run the dashboard and
  verify its browser preview.
- Report the implementation and validation results, including any unresolved
  issues, as the handoff.

## Dependencies and parallel work

- The shared data schema and markup/CSS hooks must be agreed before
  implementation to prevent incompatible interfaces.
- After that contract is set, the Designer can work on `app/styles.css` in
  parallel with the Coder working on `app/project-data.json` and
  `.vscode/launch.json`.
- The Coder's `app/index.html` depends on both the agreed markup/CSS hooks and
  the JSON schema, so integrate it against those contracts. The design
  specification in `docs/project-pulse-design.md` defines the extended health,
  progress, and milestone fields. Keep the file assignments non-overlapping.
- Final integration and runtime validation happen sequentially after the
  assigned files are ready.

## Edge cases and design decisions

- Serve the app over HTTP rather than opening the HTML file directly, so the
  browser can fetch `project-data.json`.
- Handle a failed or invalid data response visibly; do not leave a blank
  dashboard or silently show success-shaped content.
- Use readable status and priority text in addition to color so meaning remains
  available to users with color-vision differences.
- Use semantic headings and labels, sufficient text/background contrast, and
  keyboard-accessible content. The dashboard is static, so avoid adding
  interactions that are not needed for the brief.
- Keep project data local and deterministic; no API, secrets, or network
  service is needed.
- The brief does not prescribe exact sample projects, color palette, or
  typography. Designer and Coder can choose sensible representative content
  and a cohesive visual theme while preserving the required fields and
  contributor-first information hierarchy.

## Validation

- Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and
  `.vscode/launch.json` exist.
- Check that the HTML has the exact `Project Pulse` title, references the CSS
  and JSON files, and renders data-driven project cards with each required
  project field.
- Parse `app/project-data.json` and verify it has a top-level `projects` array
  whose entries include `name`, `owner`, `status`, `recentActivity`,
  `priority`, `health`, `progress`, and `milestones`. Verify progress values
  are valid, and milestone dates and statuses follow the design contract.
- Check that the CSS contains `.dashboard` and `.project-card` and provides
  responsive card styling, `border-radius`, and `box-shadow`.
- Parse `.vscode/launch.json` as strict JSON. Verify the exact launch name,
  server command, `app/` working directory, and browser URL ending in
  `/index.html`.
- Start **Run Project Pulse Dashboard** and confirm it opens the rendered
  dashboard rather than a directory listing. Confirm multiple project cards
  show health, progress, ownership, recent activity, and the next milestone,
  and check the narrow-screen layout.
- If practical, temporarily test a missing or malformed data response to
  confirm the visible error state, then restore the valid data file.

## Open questions

- None block implementation. Use representative project names and a cohesive
  visual theme unless Mona provides preferred examples or branding.
