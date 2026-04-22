## Title

MVP recorder: headed Playwright (Node.js) emitting storageState + flow plan + network log

## What Was Decided

Implement the MVP recorder as a headed Playwright tool in Node.js that records a user flow (starting with login) and outputs three artifacts: storageState.json, flow.plan.json, and flow.net.json.

## Rationale

A headed Playwright recorder provides the needed fidelity and control for deterministic replay, session capture, and later payload injection/validation while producing the minimum artifact set required by the downstream execution engine.

## Assumptions

- Playwright can run headed in the target environment used for recording.
- The execution/injection engine will consume storageState.json, flow.plan.json, and flow.net.json as inputs.

## Confidence

high