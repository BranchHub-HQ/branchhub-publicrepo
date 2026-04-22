## Title

Backend/agent language: Node.js (TypeScript) aligned with Playwright

## What Was Decided

Implement backend and agent/worker components in Node.js (preferably TypeScript) to align with Playwright’s first-class Node APIs and enable shared schemas/types across recorder and execution.

## Rationale

The user explicitly committed to Node.js and requested a Node.js structure; it reduces integration friction with Playwright and supports fast iteration for the MVP.

## Assumptions

- Team prefers JS/TS tooling and rapid iteration.
- Most browser automation and worker logic will be implemented using Playwright Node.

## Confidence

high