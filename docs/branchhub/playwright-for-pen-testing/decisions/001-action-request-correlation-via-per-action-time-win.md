## Title

Action→request correlation via per-action time windows and timestamps

## What Was Decided

Correlate network requests to UI actions using timestamp-based time windows: each action has an actionId and startTs; when the next action starts, close the previous window (endTs = nextAction.startTs - 1); assign each request to the action whose window contains its timestamp, with navigation represented as its own action type.

## Rationale

Time-window correlation is straightforward to implement for an MVP and provides an actionable mapping from each recorded UI step to the requests it likely triggered without relying on complex/brittle initiator tracing.

## Assumptions

- UI event timestamps and network event timestamps are captured with sufficient precision and comparable clock source.
- Most action-triggered requests occur shortly after the action during typical user-driven flows.

## Pros

- Simple, fast to implement, and easy to reason about.
- Works well for sequential user flows and many SPA patterns.
- Produces a clean actionId → requests mapping usable by the injection/validation engine.

## Cons

- May misattribute background polling, prefetching, or long-running async requests.
- Lower precision than initiator/trace-based attribution in highly concurrent apps.

## Alternatives Explored

- **DevTools initiator / stack-trace based attribution**
  - Reason Rejected: More accurate but significantly more complex and brittle for an MVP.
- **Signature/content-based heuristics (URL/body matching)**
  - Reason Rejected: Potentially useful as a supplement later, but not as clean or deterministic as a window model for MVP.

## Confidence

high