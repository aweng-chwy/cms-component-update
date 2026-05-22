# CMS Component Update Workflow

## Inputs

- Authoring change summary, including new fields/options/validation from `content-authoring-ui` and `content-authoring-backend`.
- content-a API contract when available, such as schema docs, sample JSON payloads, fixture updates, endpoint output, or linked content-a PRs.
- Figma/design source when available.
- Affected widget type and likely `cms-template-library` package.
- Active scope, priority level, explicit non-goals, and any follow-up prompt corrections.
- Linked UI/backend PRs or local branches.
- Expected runtime rendering behavior and acceptance criteria.
- Responsive behavior by viewport, including overlay/component requirements.
- Fallback behavior when new data is missing from existing content.
- Requested branch, PR title/body requirements, base branch, and JIRA ticket.

## Intake Output

```markdown
## CMS Component Update Intake
- Authoring source:
- content-a API contract:
- Figma/design source:
- Affected package/component:
- Active scope:
- Deferred / out of scope:
- New or changed data contract:
- Provisional data-shape assumptions:
- Expected rendering behavior:
- Backward compatibility behavior:
- Responsive states:
- Overlay / interaction matrix:
- Branch / PR instructions:
- Linked UI/backend PRs:
- Open questions:
```

## Scope Reconciliation Gate

Before each implementation pass and again before PR prep, restate the latest active scope from the user and design sources:

- Current P0 requirements and acceptance criteria.
- Explicit non-goals, deferred P1s, and superseded earlier instructions.
- Supported data count and variants, such as single vs multiple messages.
- Required interaction components, such as Chirp modal, bottom sheet, drawer, or inline expansion.
- Requested branch name, PR title, draft/open state, and required PR wording.

If a follow-up narrows the scope, remove already-added behavior when practical or isolate it behind non-rendered fixtures only when removal is riskier. Do not keep future-ready behavior just because it was implemented earlier.

## Repo Relationship Map

- `cms-template-library`: primary repo for CMS components. Update packages, typings, SCSS, stories, tests, mocks, and universal routing here.
- `content-a`: runtime delivery API and source of truth for CMS component payloads. Prove render data shape from this repo, a sample payload, fixture, endpoint output, generated type, or linked content-a PR before coding when shape is unclear.
- `content-authoring-ui`: authoring forms, dialogs, previews, and generated client types. Use as context for what authors configure, not as proof of runtime delivery shape.
- `content-authoring-backend`: authoring schema, resolvers, validation, persistence, and generated artifacts. Use as context for stored fields and widget/item contracts.
- `content-authoring-c`: APIs that `content-authoring-backend` requests. Inspect this repo when backend behavior or field availability depends on an upstream authoring API contract, response shape, or service behavior.

## Decision Table

| Finding | Path |
| --- | --- |
| Authoring change does not affect rendered output | Stop; no template-library change is needed. |
| Existing component already supports the field/option | Add or update tests/stories only if coverage is missing. |
| Component prop/type is missing the field | Update `typings.ts` or established type location, then render logic and tests. |
| New dropdown/enum option changes visual behavior | Add option handling, fallback behavior, stories, and tests. |
| New field requires styling/layout | Update component SCSS module with scoped changes and responsive checks. |
| New widget has no package | Create a package using the new package checklist, then wire stories, mocks, tests, and universal routing only if required. |
| Universal component routing is affected | Update package exports/universal mapping and add coverage. |
| Build or runtime fails with a Yarn PnP missing dependency | Add the runtime import to the package's own `dependencies` and verify the built package can load. |
| content-a API contract is available | Use it as the runtime data source of truth before editing component props/rendering. |
| content-a API contract is unavailable but sample payloads exist | Use samples/fixtures as provisional evidence and document assumptions. |
| User explicitly authorizes an assumed contract | Create the narrowest provisional type/fixture, document assumptions in stories/tests/PR, and keep it easy to replace when content-a lands. |
| Delivery data shape is unclear and assumptions are not authorized | Stop and ask for the content-a API contract, sample payload, fixture, or linked content-a PR before implementation. |
| User says to ignore P1 or future behavior | Remove it from active scope and do not implement it, even if it appears in design notes. |
| Prompt says not to touch an old component | Add a new package or isolated entry point, then verify the legacy path has no diff before committing. |
| Specific Chirp components are required for interaction | Use the required Chirp component, declare the owning package dependency, and add tests/stories proving it renders for the intended state. |

## New Package Checklist

When adding a new `cms-template-library` package, include:

- `packages/<name>/package.json` with all runtime imports in `dependencies`.
- `src/typings.ts`, `src/index.ts`, main component file, and scoped SCSS module.
- Storybook stories using realistic CMS/content-a payloads, not design-only props.
- Package tests covering rendering, fallback behavior, and primary interactions.
- A mock file under the repo's established mock location when universal or Storybook uses one.
- Universal package dependency, type imports, lazy import, switch/case routing, styles import, stories, and tests when the widget is rendered through universal.
- Storybook app dependency and MSW/story selection support when the package should appear in shared Storybook.
- README/changelog only if the repo convention for sibling packages requires them.

## Responsive Figma Matrix

For visual or tile/card work, record the Figma measurements before changing CSS:

- device state: small, medium, large, or named viewport
- container width/height
- tile/card count and grouping
- tile/card x/y positions
- tile/card width/height
- image area dimensions and object-fit behavior
- text visibility rules and empty-copy behavior
- scroll behavior and whether controls/chrome are shown

Use this matrix to implement CSS. Do not infer medium or large layouts from existing CSS if Figma provides explicit viewport nodes.

## Responsive Overlay Matrix

For modals, bottom sheets, drawers, accordions, or expanded details, record the behavior before editing:

| Viewport | Breakpoint source | Required component | Expected behavior | Test/story |
| --- | --- | --- | --- | --- |
| small | design/token/code value | e.g. `KibSheet` | opens from bottom | story/test name |
| medium | design/token/code value | e.g. `KibModal` | centered dialog | story/test name |
| large | design/token/code value | e.g. `KibModal` | centered dialog | story/test name |

Prefer intent-based names for this logic, such as `DetailsOverlayViewport` or `shouldUseBottomSheet`, rather than naming it after the whole banner/component when it only controls an interaction. Add focused tests that assert the intended component for each distinct responsive behavior.

## Compact Surface Height Guard

For banners, nav bars, promo strips, headers, or other compact global surfaces:

- Treat the simplest existing variant, such as text-only, as the closed-state height baseline unless the requirement says otherwise.
- Do not let optional icon, CTA, or expandable-detail support increase the closed-state height.
- Compare responsive closed-state height in stories, screenshots, or DOM assertions when practical.
- Keep expanded content height changes inside the required overlay/interaction component.

## Dependency And PnP Checks

- After adding a package or import, verify every runtime import is declared in that package's `dependencies`, not only hoisted or available elsewhere in the repo.
- For built CommonJS packages, run a package-level load check when practical, such as `yarn workspace <package> exec node -e "require('./dist/common.js')"`.
- If the load check reports a Yarn PnP missing-dependency error, add the missing package to the owning package manifest and rerun install/build as needed.

## Implementation Steps

1. Locate the package:
   - Search by widget type, schema name, component name, and story names.
   - Inspect `src/index.ts`, `src/typings.ts`, main component file, stories, tests, and styles.
2. Map content-a contract to runtime:
   - field names and type
   - required vs optional
   - default/fallback behavior
   - supported enum/dropdown values
   - analytics or link behavior
   - accessibility and alt text impact
   - Figma props that are design-only and should not become runtime API
3. Implement rendering:
   - update types first
   - keep active scope and deferred requirements visible while editing
   - keep missing-field fallback stable
   - add option handling with explicit defaults
   - update styles only for the changed visual behavior
   - update package exports or universal mapping only when required
   - choose names based on the behavior being controlled, not the broad component name
4. Update examples:
   - add a story for the new field/option
   - use realistic CMS/content-a payloads in stories and mocks
   - update fixtures/mock content
   - add tests for new rendering and fallback behavior
   - update snapshots only when the visual output intentionally changes
5. Verify:
   - prefer package-level tests when possible
   - run lint/build when shared types, exports, or styles changed
   - run a package load/PnP check after adding new runtime dependencies
   - verify legacy component paths have no diff when the user asked not to touch them
   - verify responsive interaction behavior for every row in the overlay matrix
   - verify compact closed-state height for banner/header-like surfaces when applicable
   - record commands and results in the PR body
6. Prepare PR:
   - use `references/pr-template.md`
   - include Figma/design source, content-a API contract evidence or provisional assumptions, linked authoring PRs, active scope, deferred items, contract summary, test evidence, and backward compatibility notes
   - honor requested branch name, draft state, title, and required wording such as "created by the skill"
   - confirm only scoped files are staged; leave unrelated dirty files unstaged
   - show title/body before commit, push, or PR creation unless the user explicitly requested direct PR creation

## Suggested Commands

Start with repository-specific commands from `package.json` and package configs.

Common checks:

```bash
yarn test
yarn lint
yarn build
```

Package-focused checks may be available through Lerna or the package's own `package.json`; inspect before running broad commands.

## Prompt Template

```text
Use $cms-component-update to update the CMS component for these authoring changes.

Authoring change summary:
content-a API contract or sample payload:
Figma/design source:
Affected widget/package:
Linked content-authoring-ui PR:
Linked content-authoring-backend PR:
Expected rendering behavior:
Fallback behavior for existing content:
JIRA ticket:
Base branch:

Rules:
- Do not mutate CMS records.
- Do not change authoring UI/backend unless separately approved.
- Ask for the content-a API contract when available and use it as the runtime source of truth.
- Preserve backward compatibility when new data is absent.
- Show PR title/body before commit, push, or PR creation.
```
