# CMS Component Update Workflow

## Inputs

- Authoring change summary, including new fields/options/validation from `content-authoring-ui` and `content-authoring-backend`.
- content-a API contract when available, such as schema docs, sample JSON payloads, fixture updates, endpoint output, or linked content-a PRs.
- Figma/design source when available.
- Affected widget type and likely `cms-template-library` package.
- Linked UI/backend PRs or local branches.
- Expected runtime rendering behavior and acceptance criteria.
- Fallback behavior when new data is missing from existing content.
- Base branch and JIRA ticket.

## Intake Output

```markdown
## CMS Component Update Intake
- Authoring source:
- content-a API contract:
- Figma/design source:
- Affected package/component:
- New or changed data contract:
- Expected rendering behavior:
- Backward compatibility behavior:
- Responsive states:
- Linked UI/backend PRs:
- Open questions:
```

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
| Delivery data shape is unclear | Stop and ask for the content-a API contract, sample payload, fixture, or linked content-a PR before implementation. |

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
   - keep missing-field fallback stable
   - add option handling with explicit defaults
   - update styles only for the changed visual behavior
   - update package exports or universal mapping only when required
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
   - record commands and results in the PR body
6. Prepare PR:
   - use `references/pr-template.md`
   - include Figma/design source, content-a API contract evidence, linked authoring PRs, contract summary, test evidence, and backward compatibility notes
   - show title/body before commit, push, or PR creation

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
