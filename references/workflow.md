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
| Universal component routing is affected | Update package exports/universal mapping and add coverage. |
| content-a API contract is available | Use it as the runtime data source of truth before editing component props/rendering. |
| content-a API contract is unavailable but sample payloads exist | Use samples/fixtures as provisional evidence and document assumptions. |
| Delivery data shape is unclear | Stop and ask for the content-a API contract, sample payload, fixture, or linked content-a PR before implementation. |

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
3. Implement rendering:
   - update types first
   - keep missing-field fallback stable
   - add option handling with explicit defaults
   - update styles only for the changed visual behavior
   - update package exports or universal mapping only when required
4. Update examples:
   - add a story for the new field/option
   - update fixtures/mock content
   - add tests for new rendering and fallback behavior
   - update snapshots only when the visual output intentionally changes
5. Verify:
   - prefer package-level tests when possible
   - run lint/build when shared types, exports, or styles changed
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
Use $cms-component-update to update the CMS website component for these authoring changes.

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
