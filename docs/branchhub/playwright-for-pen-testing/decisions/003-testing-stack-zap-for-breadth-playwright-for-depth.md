## Title

Testing stack: ZAP for breadth + Playwright for depth and exploit validation

## What Was Decided

Use ZAP/HAR/OpenAPI-based scanning for discovery and broad coverage, while treating Playwright as an equally important layer for real-browser execution, authentication/business-logic flows, and exploit validation (e.g., DOM XSS).

## Rationale

ZAP provides cheap breadth but cannot reliably execute complex JS-driven app behavior or validate certain vulnerabilities; Playwright provides high-fidelity browser execution to reduce false positives and cover SPA/auth flows.

## Assumptions

- ZAP will be used for crawling/discovery and parameter fuzzing where feasible.
- Playwright runs will be targeted to flows/endpoints where real-browser validation matters (XSS, auth, business logic).

## Pros

- Improves confidence by confirming issues in a real browser.
- Covers DOM XSS and SPA behaviors that proxy-based tooling can miss.
- Balances cost: ZAP for breadth, Playwright for depth.

## Cons

- Requires integrating and orchestrating two toolchains.
- Playwright execution is resource-intensive compared to proxy-only scanning.

## Alternatives Explored

- **ZAP-only approach**
  - Reason Rejected: Cannot reliably validate DOM/JS-driven vulnerabilities and complex auth/business logic issues.
- **Playwright-only approach**
  - Reason Rejected: Too expensive for broad discovery; misses efficient endpoint discovery and fuzzing breadth.

## Confidence

medium