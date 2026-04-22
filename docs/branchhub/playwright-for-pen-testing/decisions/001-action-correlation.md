## Action Correlation

Title: Action→request correlation via per-action time windows and timestamps

## What Was Decided

Correlate network requests to UI actions using timestamp-based time windows: each action has an actionId and startTs; when the next action starts, close the previous window (endTs = nextAction.startTs - 1); assign each request to the action whose window contains its timestamp, with navigation represented as its own action type.

## Rationale

Time-window correlation is straightforward to implement for an MVP and provides an actionable mapping from each recorded UI step to the requests it likely triggered without relying on complex/brittle initiator tracing.

## Assumptions

- UI event timestamps and network event timestamps are captured with sufficient precision and comparable clock source.
- Most action-triggered requests occur shortly after the action during typical user-driven flows.

## Confidence

high
