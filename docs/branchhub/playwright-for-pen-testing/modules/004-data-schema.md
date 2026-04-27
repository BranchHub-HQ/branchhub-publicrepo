## Module Overview
Purpose: define the data schemas produced by the recorder (storageState.json, flow.plan.json, flow.net.json), and the correlation algorithm that maps network requests to UI actions for later injection/validation. Scope: MVP recorder v0 (headed Playwright + optional extension recorder) and backend ingestion for deterministic replay.

## Architecture
- Recorder (headed Playwright or browser extension) emits three artifacts.
- Uploader posts artifacts to backend.
- Backend ingests, correlates requests→actions, and stores actionable mappings for Playwright workers.

```mermaid
graph TD
  A[User Recording (Playwright / Extension)] --> B[Recorder Artifacts]
  B --> C[Uploader -> Backend API (/upload-flow)]
  C --> D[Correlation Engine]
  D --> E[Storage / Execution Worker Queue]
  E --> F[Playwright Execution Worker]
```

Sequence: Recorder -> POST /upload-flow -> Correlation Engine computes mappings -> stored artifacts used by executor.

## Implementation Details

### Minimal artifact schemas (examples)

flow.plan.json (action timeline)
```json
{
  "actions": [
    {
      "actionId": "a1",
      "type": "click",          // click|fill|select|navigation
      "startTs": 1680000001000,
      "endTs": 1680000001500,   // closed when next action starts: next.startTs - 1
      "frame": "main",
      "locators": {
        "primary": "data-testid=submit",
        "fallback": "button[type=submit]"
      },
      "value": null
    },
    {
      "actionId": "a2",
      "type": "fill",
      "startTs": 1680000002000,
      "value": "alice@example.com"
    }
  ]
}
```

flow.net.json (HAR++-style minimal events)
```json
{
  "requests": [
    {
      "id": "r1",
      "startTs": 1680000001200,
      "method": "POST",
      "url": "https://api.example.com/login",
      "headers": { "content-type": "application/json" },
      "postData": "{\"user\":\"...\"}", // truncated/optional
      "response": {
        "status": 200,
        "contentType": "application/json",
        "endTs": 1680000001250
      },
      "reqSignature": "POST|/login|<body-hash?>"
    }
  ]
}
```

storageState.json
- Use Playwright storageState format (cookies/localStorage/sessionStorage). Treat as opaque input to the executor; ensure secure handling.

### Request→Action Correlation (time-window algorithm)
Decision referenced: "Action→request correlation via per-action time windows and timestamps"

Algorithm summary:
- Each action has startTs. For action i, endTs = action[i+1].startTs - 1. Last action open-ended (or uses recording endTs).
- Assign request r to action a if a.startTs <= r.startTs <= a.endTs.
- Treat navigation actions as their own action type so navigations map to the requests that load the page.

JS example:
```javascript
function correlate(actions, requests) {
  // assume actions sorted by startTs
  for (let i=0;i<actions.length;i++) {
    actions[i].endTs = (i+1 < actions.length) ? actions[i+1].startTs - 1 : Infinity;
    actions[i].requests = [];
  }
  for (const r of requests) {
    const a = actions.find(act => act.startTs <= r.startTs && r.startTs <= act.endTs);
    if (a) a.requests.push(r.id);
    else /* background/polling */ assignToFallback(r);
  }
  return actions;
}
```

Fallbacks: requests not in any window may be flagged as background/polling; optional later improvement: initiator/stack-trace attribution.

## API
POST /upload-flow
- Body: multipart/json { storageState.json, flow.plan.json, flow.net.json, meta }
- Response: 202 Accepted with flowId and correlation summary (counts per action)

Example response:
```json
{ "flowId":"f123", "actions": 12, "requests": 34, "correlated": 30 }
```

## Related Decisions
- Action→request correlation via time-window (decision_key: 131b1ee5-...) — chosen for simplicity and MVP speed.
- MVP recorder as headed Playwright emitting three artifacts (decision_key: 56cecc55-...) — defines artifact contract.
- Recorder v0 action coverage (click/fill/select/navigation) (decision_key: c482138a-...) — limits recorded actions and preserves final fill values.

## Key Technical Details
- Dependencies: Playwright (recorder & executor), Node.js/TypeScript backend. Optional extension uses chrome.webRequest/devtools.
- Data structures: compact action timeline (indexed by actionId), HAR++-style request events with reqSignature.
- Integration points: uploader endpoint, correlation engine, execution worker queue.
- Privacy/security: storageState.json contains secrets; enforce encryption/retention policies at ingestion.