# CMS Component Update

Codex skill for updating CMS components when authoring changes introduce new fields, options, or rendering behavior.

This skill is intended for non-technical CMS and content partners who need authoring changes reflected in `cms-template-library` without manually translating those changes into React implementation details.

## Use It

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
```

## What It Does

- Maps CMS authoring changes to the CMS components that render content-a API data.
- Updates `cms-template-library` React components, typings, styles, stories, fixtures, and tests when needed.
- Uses the content-a API contract or sample payload as the runtime source of truth.
- Preserves existing content behavior when new fields are missing.
- Prepares a component update PR body using the bundled agent PR template.

## When To Use It

- A CMS dropdown gets a new option that changes component rendering.
- A new CMS field needs to appear in a rendered component.
- A Figma change adds a visual state that depends on CMS data.
- An authoring UI/backend PR exists, and the CMS component also needs to support the new field or option.

## What To Provide

- The authoring change summary in plain language.
- The content-a API contract, sample JSON payload, fixture, endpoint output, or linked content-a PR when available.
- The Figma link or design source, if there is one.
- The affected widget, component, package, or page area.
- Links to related `content-authoring-ui` and `content-authoring-backend` PRs.
- Expected rendering behavior and fallback behavior for older content.

## Guardrails

- Does not create, update, schedule, or publish CMS records.
- Does not upload assets to Bynder.
- Does not change `content-authoring-ui` or `content-authoring-backend` unless separately approved.
- Does not treat authoring UI/backend PRs as proof of the runtime data shape.
- Stops and asks for the content-a contract, sample payload, fixture, or linked content-a PR when the delivered data shape is unclear.
- Shows PR title and body before commit, push, or PR creation.

## Files

- `SKILL.md`: trigger metadata and core operating instructions.
- `references/workflow.md`: detailed workflow, intake format, decision table, and prompt template.
- `references/pr-template.md`: `cms-template-library` PR body template.
- `agents/openai.yaml`: UI metadata for Codex skill discovery.

## Install Location

This repo is intended to live under:

```text
~/.codex/skills/cms-component-update
```
