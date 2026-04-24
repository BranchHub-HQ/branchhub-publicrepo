## Title

Recorder v0 captures only click/fill/select/navigation; record final fill value on blur/change

## What Was Decided

For Recorder v0, record only click, fill, select, and navigation actions; for fills, capture the final value on blur/change rather than every keystroke. Also capture frame context and store multiple locator forms (primary + fallback).

## Rationale

These events cover the core security-relevant interaction surface (inputs and triggers) while minimizing noise, artifact size, and privacy exposure, and they make action→request correlation more reliable.

## Assumptions

- Most security-relevant requests are triggered by click/fill/select/navigation actions.
- Final field values on blur/change are sufficient for later injection and replay correctness.

## Pros

- Lower-noise recordings that are easier to replay and correlate to network activity.
- Smaller artifacts and reduced sensitive data capture versus keystroke-level logging.
- Explicit injection points (fills/selects) are preserved.

## Cons

- May miss edge-case triggers tied to other UI events (e.g., keyboard shortcuts, file uploads, hover-driven actions).
- Intermediate UI-only state changes are not captured in v0.

## Alternatives Explored

- **Capture scrolling/hover/every keystroke**
  - Reason Rejected: Adds significant noise and volume with limited MVP security value.
- **Capture a broader event set in v0 (keyboard, file upload, modal state)**
  - Reason Rejected: Increases complexity; can be added incrementally after the end-to-end pipeline works.

## Confidence

high