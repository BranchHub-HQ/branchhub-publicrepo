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

## Pros

- Fast time-to-MVP by avoiding AI research and UI build-out.
- Deterministic per-build replay improves reliability and debuggability.
- Validates the hardest/highest-value capability first (auth flows, injection, detection).
- Defers complex automation (Git-driven script generation/maintenance) until after core engine works.

## Cons

- Less differentiation initially without AI features.
- No polished end-user UI early; adoption may require technical users.
- Per-build approach may miss new flows unless recordings are updated; script maintenance is deferred.

## Alternatives Explored

- **Include AI intelligence layer in MVP**
  - Reason Rejected: Significantly increases scope, research complexity, and time-to-market before core exploit validation is proven.
- **Build UI concurrently with the engine**
  - Reason Rejected: Slows down proving core functionality and risks wasted UI work if engine design changes.
- **Autonomous discovery and script generation at MVP**
  - Reason Rejected: Too complex and fragile for MVP; deterministic recording/replay is simpler and more repeatable.

## Confidence

high