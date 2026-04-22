## Title

Recorder v0 captures only click/fill/select/navigation; record final fill value on blur/change

## What Was Decided

For Recorder v0, record only click, fill, select, and navigation actions; for fills, capture the final value on blur/change rather than every keystroke. Also capture frame context and store multiple locator forms (primary + fallback).

## Rationale

These events cover the core security-relevant interaction surface (inputs and triggers) while minimizing noise, artifact size, and privacy exposure, and they make action→request correlation more reliable.

## Assumptions

- Most security-relevant requests are triggered by click/fill/select/navigation actions.
- Final field values on blur/change are sufficient for later injection and replay correctness.

## Confidence

high