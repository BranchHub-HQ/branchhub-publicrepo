## Title

MVP recorder: headed Playwright (Node.js) emitting storageState + flow plan + network log

## What Was Decided

Implement the MVP recorder as a headed Playwright tool in Node.js that records a user flow (starting with login) and outputs three artifacts: storageState.json, flow.plan.json, and flow.net.json.

## Rationale

A headed Playwright recorder provides the needed fidelity and control for deterministic replay, session capture, and later payload injection/validation while producing the minimum artifact set required by the downstream execution engine.

## Assumptions

- Playwright can run headed in the target environment used for recording.
- The execution/injection engine will consume storageState.json, flow.plan.json, and flow.net.json as inputs.

## Pros

- High-fidelity recording aligned with later Playwright-based replay/execution.
- Captures authenticated state via storageState for deterministic runs.
- Produces minimal, structured artifacts suitable for upload and later injection.

## Cons

- Heavier than an extension-only recorder and requires local headed browser resources.
- Does not by itself address scaling; separate worker/orchestration will be needed later.

## Alternatives Explored

- **Chrome extension only (record + execute in-browser)**
  - Reason Rejected: Useful for UX, but limited for execution/injection control and harder to scale reliably.
- **Playwright codegen-based recorder**
  - Reason Rejected: Less aligned with manual interaction capture and stable locator strategy desired for this MVP.

## Confidence

high