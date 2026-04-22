## Title

MVP scope: deterministic per-build replay, engine-first, no AI (UI later; Git automation later)

## What Was Decided

The MVP will focus on a deterministic, engine-first system: record/replay/inject/validate per build, without an AI intelligence layer and without a full user-facing UI. Git-driven script update automation is deferred to a later phase.

## Rationale

This reduces scope and risk while proving the core value (reliable replay + payload injection + exploit validation) quickly; per-build determinism makes runs repeatable, and AI/UI/Git automation can be layered on once the engine is stable.

## Assumptions

- Recorded flows remain stable enough within a given build to allow repeatable replays.
- Core value can be demonstrated without AI-driven exploration or payload generation.
- A UI can be added later once engine APIs and artifacts stabilize.
- Git-based detection of code changes and auto-updating scripts can be added later without blocking MVP value.

## Confidence

high