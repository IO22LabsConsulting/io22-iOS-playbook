# App Architecture for Small Teams

Architecture quality decides whether a small team can keep shipping after the
first release. The goal is not elegance. It is that any one change stays small.

## Core Architecture Pattern

Use four clear layers:

1. Presentation layer: SwiftUI screens and reusable components
2. Domain layer: pure logic for rules, scoring and recommendations, with no UI or network imports
3. Data layer: repositories for persistence and synchronization
4. Service layer: network, storage, sensors, telemetry, each behind a protocol

This separation keeps a change in one layer from destabilizing the others.

## Testable Without a Network

- Every service is a protocol. ViewModels receive their dependencies in `init`.
- One mock client captures requests and replays a queue of stubbed responses,
  so the whole app runs in tests with no network.
- Anything that touches money or user data gets a unit test before it gets a screen.

## Reliability First

Users forgive lower sophistication before they forgive instability.

Prioritize:

- Predictable performance budgets for the hot path
- Graceful fallback when the app is unsure or offline
- Deterministic handling for unsupported devices and OS versions
- Logging for dropped work and latency, with no personal data in the log line

## Offline Is Normal

Network drops happen at the worst moment. If a core action must work offline,
give it a durable local queue, and show the user the queue's depth and age. Age
is the signal: one item that is hours old means a device that quietly stopped
talking.

## Privacy By Design

- Prefer on-device processing
- Minimize storage by default
- Add explicit consent gates for telemetry
- Offer deletion and export controls in settings

Trust is a product feature. Architecture should enforce it.
