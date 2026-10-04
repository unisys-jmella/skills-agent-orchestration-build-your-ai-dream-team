# Project Pulse dashboard design

## Design goal

Give contributors a fast, trustworthy read on project health: what is moving,
who owns it, what changed recently, and which milestone needs attention next.
Keep the dashboard static and lightweight, with a single responsive page and
local JSON data.

## Page hierarchy

1. **Dashboard header:** “Project Pulse” title, a one-line description, and a
   small “Updated” timestamp only if the data provides one. Avoid implying live
   updates when the app is static.
2. **Portfolio summary:** compact totals for projects, at-risk/off-track
   projects, and upcoming milestones. Derive counts from the loaded project
   data rather than maintaining duplicate values.
3. **Project collection:** a responsive grid of project cards. Use
   `.dashboard` as the page content container and `.project-card` for each card.
4. **Project card:** name and health first; owner and progress next; recent
   activity and the next milestone as supporting details.

The first viewport should show the title, portfolio summary, and the start of
the project collection. Keep the header compact so the projects remain the
visual focus.

## Project card content model

Each card presents information in this order:

- **Project name** as the card heading.
- **Health** as a text label with a small semantic indicator: `On track`,
  `At risk`, or `Off track`. Use both text and color; never rely on color alone.
- **Owner** as a clearly labeled person or team. If an avatar is used, treat it
  as decorative unless meaningful alternative text is available.
- **Progress** as a labeled progress bar with a visible percentage and
  completed/total context (for example, “6 of 10 milestones · 60%”). Use a
  determinate value based on project data, not an inferred health score.
- **Recent activity** as a concise sentence with an optional date when
  available. Make it readable without requiring hover.
- **Next milestone** with its name, due date, and status (for example,
  `Upcoming`, `In progress`, `Complete`, or `At risk`). Show a clear empty
  state when there is no upcoming milestone.

Keep status, priority, and health distinct: status describes the work phase,
priority describes relative importance, and health describes delivery
confidence. Show the existing project status and priority as secondary labels
where they add context without competing with health.

## Data contract additions

Preserve the existing `projects` array fields: `name`, `owner`, `status`,
`recentActivity`, and `priority`. Add these fields to support the requested
health, progress, and milestone views:

- `health`: one of `on-track`, `at-risk`, or `off-track`.
- `progress`: an object with numeric `completed` and `total` values. Require
  `total` to be greater than zero and `completed` to be between zero and total.
- `milestones`: an array of objects with `name`, `date` (ISO `YYYY-MM-DD`),
  and `status` (`upcoming`, `in-progress`, `complete`, or `at-risk`).

For the card, use the earliest non-complete milestone as the next milestone.
If every milestone is complete or the list is empty, show a neutral “No upcoming
milestone” message. If optional activity dates are later added, keep them
separate from the existing `recentActivity` summary string.

## Visual direction

- Use a calm, light neutral page background with white or near-white cards,
  dark high-contrast text, and one restrained accent color for links and
  progress.
- Use consistent spacing and clear typographic levels: page title, card
  heading, then compact labeled metadata.
- Give cards a subtle border, rounded corners, and restrained shadow. Avoid
  heavy decoration that obscures health or milestone information.
- Use distinct health treatments (for example, green, amber, and red) with
  explicit labels and accessible contrast. Keep priority visually secondary.
- Make progress bars visually quiet and include their numeric value as text.

## Responsive behavior

- Wide screens: use a three-column card grid when space permits.
- Medium screens: use two columns.
- Narrow screens: stack cards in one column; let labels and milestone dates wrap
  naturally without horizontal scrolling.
- Keep card content in the same reading order at every width. Do not rely on
  hover, side-by-side-only relationships, or fixed card heights.

## Accessibility and states

- Use semantic page and section headings and a heading for each project card.
- Expose progress with a labeled native `<progress>` element or an equivalent
  `role="progressbar"` with correct minimum, maximum, and current values.
- Announce health and milestone state in text and pair iconography with labels.
- Maintain visible keyboard focus for any links or controls; do not add
  nonessential controls to this static dashboard.
- Respect reduced-motion preferences if transitions are added.
- Provide a loading state while JSON is fetched and a visible, understandable
  error state if the file cannot be loaded or its values are invalid.
- Handle invalid progress safely: do not render a misleading percentage or
  progress bar when values are missing, non-numeric, or out of range.

## Assumptions

- A project can have zero or more milestones, and milestone dates are calendar
  dates in ISO format.
- Health is supplied explicitly as project data; it is not automatically
  calculated from progress because percentage complete alone does not indicate
  delivery risk.
- Summary counts are derived from the loaded data. No API, charting library, or
  external asset service is needed.
