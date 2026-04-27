## Module Overview
Purpose: Headed Playwright recorder (Node.js) for MVP that records an authenticated user flow and emits three artifacts: `storageState.json`, `flow.plan.json`, and `flow.net.json`. Scope: capture security-relevant UI actions (click, fill, select, navigation), collect a compact HAR++-style network log, and timestamp actions to enable request→action correlation for later replay and payload injection.

## Architecture
- Recorder: headed Playwright process launched locally (developer machine or agent).
- Artifacts: storageState (Playwright session), flow.plan (action timeline + locators/values), flow.net (network events/HAR++).
- Uploader: sends artifacts to backend worker/orchestrator for deterministic replay/injection.
- Optional Recorder UX: Chrome extension (for in-browser UX) that can upload same artifact contract to backend.

```mermaid
graph TD
  User[User (interacts with headed browser)] --> Recorder[Headed Playwright Recorder]
  Recorder --> Storage[storageState.json]
  Recorder --> Plan[flow.plan.json]
  Recorder --> Net[flow.net.json]
  Recorder --> Uploader[Uploader / API client]
  Uploader --> Backend[Backend Playwright Workers]
  Backend --> Results[Replay + Injection + Validation]
```

## Implementation Details

### Technical approach
- Run Playwright in headed mode to capture real DOM, JS, and network behavior.
- Record only four action types for v0: `click`, `fill` (final value on blur/change), `select`, `navigation`.
- Capture locators as multiple fallback forms (primary: data-testid/aria/role; fallback: CSS; optional text).
- Capture network events in a compact HAR++ schema (minimal request/response fields; optional/truncated bodies).

### Artifacts (examples)
flow.plan.json (action examples):
```json
{
  "actions": [
    {"actionId":"a1","type":"click","ts":1710840000000,"selector":{"primary":{"kind":"testId","value":"login-btn"}}},
    {"actionId":"a2","type":"fill","ts":1710840000500,"selector":{"primary":{"kind":"ariaLabel","value":"Email"}},"value":"user@example.com"},
    {"actionId":"a4","type":"navigation","ts":1710840002000,"url":"https://example.com/dashboard"}
  ]
}
```

storageState.json: Playwright-native storage state to rehydrate cookies/localStorage.

flow.net.json: compact network entries with fields like `id`, `request.url`, `request.method`, `startTs`, `response.status`, `endTs`, and optional `reqSignature` (method + normalized URL + body hash).

### Request→Action Correlation (algorithm)
- Each action has `startTs`. When next action starts, previous action window closes at `endTs = next.startTs - 1`.
- Assign each network request whose timestamp falls within an action window to that actionId.
- Navigations are explicit actions and may form their own windows.
- Notes: this is simple/time-window-based (Decision: time-window correlation). It works well for sequential flows; polling/prefetch may be misattributed.

### Code hooks / listeners
- Use Playwright's request/response events (page.on('request'), page.on('response')) to capture network.
- Add DOM listeners in a headed context if needed to better capture element state (e.g., blur events for final fill).

Example Playwright event wiring:
```javascript
page.on('request', req => recordNetworkStart(req));
page.on('response', res => recordNetworkEnd(res));
page.on('framenavigated', frame => recordNavigation(frame.url()));
```

## Related Decisions
- Recorder v0: headed Playwright (Node.js) emitting storageState + flow.plan + flow.net. (Decision: headed Playwright MVP)
- Recorder v0 action coverage: only click/fill/select/navigation; capture final fill on blur/change.
- Correlation: time-window action→request mapping.

## Key Technical Details
- Dependencies: Playwright (Node), Node.js (TypeScript recommended), optional Chrome extension APIs for alternate recorder UX.
- Data structures: action timeline (ordered array with timestamps), compact network entries with startTs/endTs, reqSignature for deduping.
- Integration points: uploader API endpoint (POST /api/recordings/upload), backend workers that consume artifacts for deterministic replay and injection.

Keep artifacts private and handle storageState.json securely (contains auth tokens/cookies).