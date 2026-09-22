# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's **Project Pulse** dashboard as a lightweight static frontend that renders project cards from local JSON data. Each card will show the project name, owner, status, recent activity, and priority. The dashboard will be runnable from VS Code through a **Run Project Pulse Dashboard** launch configuration.

The Orchestrator will coordinate two specialists: **Designer** owns the visual and accessibility work, while **Coder** owns the HTML shell, project data, and launch configuration.

## File Assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | **Coder** | Create the semantic dashboard shell, load the stylesheet and JSON data, and render project cards. |
| `app/styles.css` | **Designer** | Create the polished responsive dashboard layout, project cards, status badges, priority treatments, contrast, and spacing. |
| `app/project-data.json` | **Coder** | Provide strict JSON with a top-level `projects` array and the required project fields. |
| `.vscode/launch.json` | **Coder** | Add strict JSON for the **Run Project Pulse Dashboard** configuration. |

No two agents will write to the same file. The Orchestrator owns coordination and integration rather than implementation.

## Shared Contract

Before implementation, the Orchestrator gives both specialists the same interface contract:

- The page title and visible heading contain `Project Pulse`.
- The data file has a top-level `projects` array.
- Every project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
- The HTML uses `.dashboard` for the main container and `.project-card` for each project card.
- The launch configuration serves from `${workspaceFolder}/app` and opens `http://localhost:%s/index.html`.

This contract prevents class-name and field-name drift between the Designer and Coder.

## Ordered Phases

### Phase 1: Interface Lock

**Owner:** Orchestrator

Freeze the shared data shape, CSS hooks, page title, and launch configuration shape. No files are written in this phase. Both specialists acknowledge the contract before implementation begins.

**Dependency:** This phase must finish before either implementation track starts.

### Phase 2: Parallel Implementation

#### Designer Responsibilities

**File scope:** `app/styles.css`

- Build a polished dashboard rather than a bare HTML page.
- Define `.dashboard` and `.project-card` selectors.
- Use `border-radius`, `box-shadow`, readable typography, clear spacing, and responsive card reflow.
- Style status and priority with sufficient contrast and non-color cues where practical.
- Support narrow screens without overlapping or unreadable content.
- Do not modify the Coder-owned files.

#### Coder Responsibilities

**File scope:** `app/index.html`, `app/project-data.json`, `.vscode/launch.json`

- Create semantic `<header>` and `<main>` landmarks with a visible `Project Pulse` heading.
- Reference `styles.css` and load `project-data.json` through a relative path.
- Render one `project-card` per project and display name, owner, status, recent activity, and priority.
- Create 3–5 realistic sample projects in strict JSON.
- Create strict JSON without comments or trailing commas for `.vscode/launch.json`.
- Add one configuration named `Run Project Pulse Dashboard`.
- Configure the launch command as `python3 -m http.server 5500`, with `cwd` set to `${workspaceFolder}/app` and a `serverReadyAction` opening `http://localhost:%s/index.html`.
- Do not modify `app/styles.css`.

**Parallel work decision:** Designer and Coder may work in parallel because their file scopes do not overlap and both use the Phase 1 contract. They must not independently change the shared class names or data fields.

### Phase 3: Integration and Validation

**Owner:** Orchestrator

After both implementation tracks finish, the Orchestrator verifies that the HTML selectors match the CSS selectors and that the JSON fields match the rendering code. Then the Orchestrator validates the files and runs the dashboard through the launch configuration.

**Dependency:** This phase waits for both Phase 2 tracks to complete.

## Dependencies

1. Phase 1 must precede all implementation so the Designer and Coder share the same contract.
2. `app/index.html` depends on `app/styles.css` for presentation and `app/project-data.json` for runtime data, but the files can be authored in parallel because the contract is already fixed.
3. `.vscode/launch.json` depends on `app/index.html` existing under `app/` when the configuration runs. Within the Coder's scope, the HTML and data should be created before runtime validation of the launch configuration.
4. Integration and validation depend on all four assigned files being present.

## Parallel Work Decisions

**Run in parallel:**

- Designer works on `app/styles.css`.
- Coder works on `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.

**Run sequentially:**

- The Orchestrator's interface lock precedes both specialists.
- The Orchestrator integrates and validates only after both specialists report completion.
- Runtime launch testing follows file and JSON validation.

## Edge Cases

- Serving from the workspace root can show a directory listing, so the launch `cwd` must be `${workspaceFolder}/app` and the URI must include `index.html`.
- Opening the HTML directly with `file://` can prevent `fetch()` from loading JSON; validation should use the launch configuration.
- Port 5500 may already be in use; stop an existing preview before relaunching or use the port reported by the server.
- Strict JSON validation must catch comments, trailing commas, and malformed project data.
- Empty or single-project data should still produce a valid responsive layout.
- Status and priority should not rely on color alone.

## Validation Expectations

### Files and syntax

- Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist.
- Run `python3 -m json.tool app/project-data.json`.
- Run `python3 -m json.tool .vscode/launch.json`.
- Check that launch JSON contains `Run Project Pulse Dashboard`, `${workspaceFolder}/app`, and `http://localhost:%s/index.html`.

### Content and integration

- Confirm `app/index.html` contains `Project Pulse`, references both `styles.css` and `project-data.json`, uses `project-card`, and renders `status`, `recentActivity`, and `priority`.
- Confirm `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- Confirm the JSON has a top-level `projects` key and all required fields for each project.
- Confirm the HTML and CSS hooks and the HTML and JSON field names match.

### Runtime

- Select **Run Project Pulse Dashboard** in Run and Debug.
- Confirm the browser opens the dashboard at `http://localhost:<port>/index.html`, not a directory listing.
- Confirm multiple project cards visibly show the required fields.
- Stop the preview server cleanly after testing.

## Open Questions

- Should priority use a fixed vocabulary such as High, Medium, and Low? A fixed vocabulary is recommended for consistent styling.
- Should sample projects use generic placeholders or Mona's real project names? Generic deterministic data is recommended.
- Are filtering, sorting, or search required? They are out of scope for the initial read-only dashboard unless requested.