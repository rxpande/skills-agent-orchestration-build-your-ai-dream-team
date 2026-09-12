# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's Project Pulse as a small, dependency-free static dashboard for contributors. The dashboard will present multiple projects in accessible, responsive cards with each project's name, owner, status, recent activity, priority or risk, and a short contributor-friendly summary.

The implementation must create:

- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`

The final app must open the dashboard UI from `index.html`, not a server directory listing.

## Responsibilities and File Assignments

| Owner | Files | Responsibilities |
| --- | --- | --- |
| Planner | `docs/project-pulse-plan.md` | Define the implementation contract, file ownership, dependencies, ordering, edge cases, and validation expectations. |
| Designer | `app/styles.css` | Establish the visual system, information hierarchy, card layout, status and priority treatment, responsive behavior, accessibility-conscious contrast, spacing, typography, rounded cards, and shadows. |
| Coder | `app/index.html` | Implement semantic page structure, reference `styles.css` and `project-data.json`, load the data, render visible project cards, and expose status, recent activity, and priority values. |
| Coder | `app/project-data.json` | Provide deterministic sample data under a top-level `projects` array. Each project must include `name`, `owner`, `status`, `recentActivity`, and `priority`; include a short summary where useful. |
| Coder | `.vscode/launch.json` | Add the strict-JSON VS Code launch configuration named `Run Project Pulse Dashboard`, serving the `app` directory and opening `index.html`. |
| Orchestrator | No implementation files | Coordinate the phases, prevent overlapping edits, review the integrated result, route corrections to the owning agent, and report validation and handoff status. |

Designer and Coder must not edit one another's assigned files. The Orchestrator should use the selectors and data contract in this plan as the integration boundary.

## Dependencies

- No external runtime package or build system is required. Use native HTML, CSS, and browser JavaScript.
- The dashboard depends on `app/project-data.json` being served over HTTP so browser `fetch()` can load it.
- The dashboard depends on `app/styles.css` for visual presentation and must reference it from `app/index.html`.
- `app/index.html` depends on the agreed `projects` data shape and CSS hooks, especially `.dashboard` and `.project-card`.
- `.vscode/launch.json` depends on Python 3 being available and must set its working directory to `${workspaceFolder}/app`.
- The launch configuration must run `python3 -m http.server 5500` and use `serverReadyAction` to open `http://localhost:%s/index.html`.
- There is no package manifest, framework, or existing frontend test suite to preserve. Repository validation is primarily workflow-based and uses JSON parsing and required-content checks.

## Ordered Implementation Steps

### 1. Confirm the implementation contract

**Owner:** Orchestrator and Planner
**Files:** `docs/project-pulse-plan.md`, repository brief, agent definitions

Use the repository brief and workflow checks to establish the required contract:

- The product name is exactly `Project Pulse`.
- The page must contain multiple visible project cards.
- Each card must expose the project's name, owner, `status`, `recentActivity`, and `priority`.
- The data file must contain a top-level `projects` key.
- The CSS must contain `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- The app must reference both `styles.css` and `project-data.json`.
- The launch name must be exactly `Run Project Pulse Dashboard`.
- The launch target must be `index.html`, not the app directory root.

### 2. Establish the visual and accessibility foundation

**Owner:** Designer
**File:** `app/styles.css`

Create a polished dashboard stylesheet with:

- A clear `.dashboard` layout and responsive project grid.
- `.project-card` styling for readable, visually distinct project entries.
- Rounded corners using `border-radius`.
- Depth and separation using `box-shadow`.
- Clear typography hierarchy for the page title, project name, metadata, activity, and summary.
- Status badges and priority or risk indicators with sufficient contrast.
- Visual treatment that does not rely on color alone; include text labels or icons where appropriate.
- Responsive behavior for narrow mobile layouts and larger screens.
- Focus-visible styles for interactive elements.
- Sensible spacing and readable line lengths.
- Optional reduced-motion handling if transitions or animations are introduced.

The Designer should report the selectors and structural assumptions that the Coder must use in `app/index.html`.

### 3. Create data and launch configuration in parallel

These tasks have separate file scopes and no dependency on the final HTML markup.

#### 3a. Define deterministic project data

**Owner:** Coder
**File:** `app/project-data.json`

Create valid JSON with a top-level `projects` array containing multiple representative projects. Every project object must include:

- `name`
- `owner`
- `status`
- `recentActivity`
- `priority`

Include realistic variation, such as active, at-risk, and completed or planning statuses, so the status and priority treatments can be reviewed. A concise `summary` field may be included to satisfy the contributor-friendly brief without changing the required fields.

Keep values bounded enough to remain readable in cards while still testing long activity text and differing priority levels.

#### 3b. Create the VS Code launch configuration

**Owner:** Coder
**File:** `.vscode/launch.json`

Create strict JSON with no comments. The configuration must:

- Use the exact name `Run Project Pulse Dashboard`.
- Run `python3 -m http.server 5500`.
- Set `cwd` to `${workspaceFolder}/app`.
- Include `serverReadyAction`.
- Use a server-ready pattern that captures the port.
- Open `http://localhost:%s/index.html`.
- Open the dashboard page directly rather than a directory listing.

### 4. Implement the dashboard page and rendering flow

**Owner:** Coder
**File:** `app/index.html`
**Depends on:** Steps 2 and 3

Build the page using semantic, accessible markup:

- A document title and visible heading containing `Project Pulse`.
- A main dashboard region using the `.dashboard` class.
- A loading state while project data is retrieved.
- A project-card template or rendering path that applies the `project-card` class to every project.
- Visible fields for each project's name, owner, `status`, `recentActivity`, and `priority`.
- A contributor-friendly summary when supplied by the data.
- Appropriate headings, labels, and `aria-live` behavior for loading and error states.
- A reference to `styles.css`.
- A reference to `project-data.json`.

Use native browser JavaScript to fetch and render the data. Insert external data as text rather than unsafe HTML so project content cannot inject markup. If the data cannot be loaded or parsed, replace the loading state with a clear, accessible error message rather than silently rendering an empty dashboard.

### 5. Integrate and review the complete result

**Owner:** Orchestrator
**Depends on:** Steps 2–4

Review all four implementation files together:

- Confirm the HTML classes match the Designer's CSS selectors.
- Confirm every required data field is rendered and visually understandable.
- Confirm data-driven rendering creates visible cards rather than only a static placeholder.
- Confirm the launch working directory, command, readiness pattern, and URL work together.
- Route CSS corrections to Designer and markup, data, or launch corrections to Coder rather than editing across ownership boundaries.
- Check that no unrelated repository files are changed.

## Parallelization Decisions

### Work that can run in parallel

After the implementation contract is agreed:

- Designer can build `app/styles.css`.
- Coder can build `app/project-data.json`.
- Coder can build `.vscode/launch.json`.

These tasks have non-overlapping file ownership and can proceed independently.

### Work that must be sequential

- The implementation contract must precede Designer and Coder work.
- `app/index.html` should be implemented after the CSS hooks and data shape are agreed, so its class names and rendering logic align with both.
- Integration review must occur after all four target files exist.
- Browser preview validation must occur after integration review.
- Any fixes must be made by the owning agent before the final validation pass.

## Edge Cases and Risk Handling

- **Direct file opening:** `fetch()` may fail under `file://`. Always validate through the launch configuration or an HTTP server.
- **Missing or malformed JSON:** Show an explicit accessible error state when the request fails or JSON parsing fails.
- **Empty `projects` array:** Render a clear "no projects available" state instead of an empty blank page.
- **Missing project fields:** Use an explicit fallback label such as "Not provided" while preserving card structure; do not silently discard the project.
- **Unknown status or priority:** Use a neutral badge style and preserve the original text rather than relying on a fixed set of CSS classes.
- **Long names or activity text:** Allow wrapping and prevent overflow on small screens.
- **Small viewport widths:** Collapse the grid to one column and retain readable spacing and touch-friendly controls.
- **Accessibility:** Preserve heading hierarchy, keyboard focus visibility, meaningful labels, sufficient contrast, and non-color-only status or priority indicators.
- **Untrusted text content:** Render project values with text-safe DOM APIs rather than interpolating raw HTML.
- **Port conflicts:** If port `5500` is occupied, stop the conflicting process before validating the prescribed launch configuration; do not silently change the required port.
- **Launch readiness:** Ensure the server-ready pattern matches Python's actual startup output so the browser opens only after the server is ready.

## Validation Expectations

### File and syntax validation

Confirm that all required files exist:

- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`

Parse both JSON files with the existing environment tooling:

```bash
python3 -m json.tool app/project-data.json
python3 -m json.tool .vscode/launch.json
```

Use `git diff --check` to catch whitespace and patch-format problems.

### Content validation

Confirm the following required content is present:

- `app/index.html` contains `Project Pulse`.
- `app/index.html` references `styles.css`.
- `app/index.html` references `project-data.json`.
- `app/index.html` uses `project-card`.
- `app/index.html` renders `status`, `recentActivity`, and `priority`.
- `app/styles.css` contains `.dashboard`.
- `app/styles.css` contains `.project-card`.
- `app/styles.css` contains `border-radius`.
- `app/styles.css` contains `box-shadow`.
- `app/project-data.json` contains the top-level `projects` key.
- Each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json` contains `Run Project Pulse Dashboard`.
- `.vscode/launch.json` contains `index.html`.
- `.vscode/launch.json` uses the `app` working directory and `python3 -m http.server 5500`.
- `.vscode/launch.json` opens `http://localhost:%s/index.html`.

### Runtime validation

Run the configured preview through **Run Project Pulse Dashboard** and verify:

- The server starts from `app/`.
- The browser opens `index.html`, not a directory listing.
- The Project Pulse heading is visible.
- Multiple project cards render from `project-data.json`.
- Status, recent activity, priority, owner, and project name are visible.
- The layout remains readable at desktop and narrow viewport widths.
- Loading, empty-data, and data-load failure states are understandable.
- Keyboard focus and text contrast remain usable.

A direct HTTP smoke check may also request `/index.html` and `/project-data.json` from port `5500` while the server is running.

The repository's Step 3 workflow is the authoritative automated check for the learner outputs; it validates required files, required phrases, JSON syntax, project fields, CSS hooks, and launch configuration content. `scripts/validate-exercise.sh` is primarily a template and infrastructure validator and intentionally expects learner answer files not to be tracked, so it should not be treated as the sole post-implementation dashboard acceptance test.

## Definition of Done

The implementation is complete when:

1. All four assigned files exist and are owned by the appropriate agent.
2. The dashboard presents real project data in visible, styled cards.
3. The app uses the required CSS hooks and data fields.
4. The app handles loading, empty, and failure states explicitly.
5. The launch configuration starts the HTTP server from `app/` and opens `index.html`.
6. JSON parsing, required-content checks, and runtime preview validation pass.
7. The Orchestrator can explain Planner, Designer, and Coder contributions and identify any remaining limitations.

## Open Questions

No blocking questions remain from the repository brief. The plan assumes:

- Native browser JavaScript is acceptable because no framework or package manager is present.
- The sample project data is local and deterministic rather than fetched from an external API.
- The required launch port remains `5500`.
- The Designer owns `app/styles.css`, while the Coder owns the HTML, data, and launch configuration to avoid overlapping edits.
