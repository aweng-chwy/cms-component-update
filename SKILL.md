---
name: cms-component-update
description: Use when CMS authoring changes must be reflected in CMS components that render content-a API data, including new widget fields, enum/dropdown options, rendering variants, typography/media behavior, Storybook stories, package tests, content-a API contract mapping, and coordinated PRs after using $figma-to-cms-authoring or similar CMS authoring work; ask for the content-a API contract when available and do not use for CMS record mutation, scheduling, Bynder uploads, or authoring-only changes that do not affect rendered output.
---

# CMS Component Update

Use this skill to carry a CMS authoring change through to CMS components in `cms-template-library`. These components are data-driven by content-a APIs, so treat the content-a API contract as the runtime source of truth. Authoring UI/backend PRs provide context, not proof of the delivered data shape.

## Quick Start

1. Read `references/workflow.md` before editing.
2. Intake the authoring change summary, content-a API contract when available, Figma/design source, affected widget/component, UI/backend PR links, and expected website behavior.
3. Locate the matching `cms-template-library/packages/<package>` component, typings, stories, tests, and styles.
4. Map the content-a API contract to component props/data shape before editing.
5. For a new widget package, complete the package/story/universal/dependency checklist in `references/workflow.md`.
6. Implement the smallest rendering change, preserving backward compatibility for existing content.
7. Add or update stories and focused tests for the new field, option, or rendering state.
8. Run the package-level test/build checks that match the touched surface.
9. Fill `references/pr-template.md`, show the PR title/body, then commit/push/open a draft PR only after confirmation.

## Non-Negotiable Gates

- Do not mutate CMS records, schedule content, publish content, or upload assets.
- Do not change `content-authoring-ui` or `content-authoring-backend` unless the user explicitly expands this task beyond the component update.
- Stop if the content-a API contract or runtime data shape is ambiguous and cannot be proven from API docs, sample payloads, code, generated types, fixtures, or live content-a examples.
- Preserve rendering behavior for existing content records where the new field is missing.
- Keep unrelated package refactors, style rewrites, dependency changes, and generated snapshot churn out of scope.
- Declare every runtime import in the package's own `dependencies`; Yarn PnP missing-dependency errors are package bugs, not test noise.
- Cross-link the authoring UI/backend PRs when this template PR depends on them, but do not treat those PRs as the runtime API contract.

## Repo Guidance

- Primary repo: `/Users/aweng/repo/cms-template-library`.
- Components live under `packages/*/src`.
- Component props/types commonly live in `typings.ts` or package-level type files.
- Stories live near components as `*.stories.tsx`.
- Tests live in `src/__tests__` and may include snapshots.
- Shared helpers and typings live under `shared/*`.

## References

- `references/workflow.md`: detailed execution workflow and decision points.
- `references/pr-template.md`: PR body template for cms-template-library component update PRs.
