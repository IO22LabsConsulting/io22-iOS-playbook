# iOS App Playbook: From Idea To App Store

A full execution framework for building a high-quality native iOS app with a
small team. Each phase has an objective, actions and deliverables. The timeline
assumes the 12-week track from [START_HERE.md](START_HERE.md); stretch or
compress it to your track.

## Phase 0: Strategy (Week 0)

### Objective
Define a narrow, winnable product scope with clear success criteria.

### Actions

1. Define one user segment and one painful workflow.
2. Write a one-page value proposition and anti-goals.
3. Define success metrics:
   - Activation: first successful value moment
   - Engagement: weekly active behavior
   - Quality: crash-free sessions and task success
4. Choose your release scope:
   - Must-have
   - Nice-to-have
   - Not now

### Deliverables

- Product brief
- MVP scope table
- Metric definitions

## Phase 1: Product Definition (Week 1)

### Objective
Turn strategy into implementation-ready requirements.

### Actions

1. Create a PRD with:
   - User stories
   - Acceptance criteria
   - Edge cases
2. Define the app's information architecture.
3. Draft UX flows for the top three user journeys.
4. Define the event taxonomy (what to track and why).

### Deliverables

- PRD v1
- User flow maps
- Event naming schema

## Phase 2: Technical Foundation (Weeks 1-2)

### Objective
Build a robust app foundation before advanced functionality.

### Recommended Architecture

- UI: SwiftUI, system components first
- State: feature-local state with clear ownership
- Domain layer: pure business logic modules with no UI or network imports
- Data layer: repository pattern over local persistence and the network
- Services: every external dependency (network, storage, sensors, telemetry)
  behind a protocol, injected in `init`, so the app is testable without a network

### Actions

1. Create app modules and folder conventions.
2. Set coding standards and lint rules.
3. Build a design token system (colors, spacing, type scale). No hardcoded values in views.
4. Implement baseline logging and crash monitoring. Wire the crash reporter so it
   starts only when configured, and scrubs personal data before anything leaves the device.
5. Add unit and UI test scaffolding, including a mock client that captures
   requests and replays stubbed responses.

### Deliverables

- Compile-safe app shell
- Shared component library
- Test harness

## Phase 3: Core Feature Build (Weeks 2-6)

### Objective
Ship one reliable value loop end to end.

### Core Loop Template

1. User starts a session.
2. App captures input.
3. App processes input locally or remotely.
4. App returns a useful result.
5. App stores the result and suggests the next action.

### If the app makes automated decisions

- Start with confidence thresholds and fallback messaging.
- Never show raw model or rule output. Convert it into plain language with a confidence level.
- Keep sensitive processing on-device when feasible.
- Log confidence bands (not the inputs) for future tuning.

### Deliverables

- End-to-end core loop
- Internal telemetry dashboard
- First real-device QA report

## Phase 4: Data, Privacy, and Compliance (Weeks 4-7)

### Objective
Operationalize trust before launch.

### Actions

1. Map every data element:
   - Collected?
   - Why needed?
   - Stored where?
   - Retention period?
2. Add consent-gated telemetry.
3. Implement export and deletion workflows. Account deletion is an App Store
   requirement if the app has accounts, and it must remove data, not just sign out.
4. Draft policy pages and in-app disclosures.
5. Validate the App Store privacy questionnaire answers against the data map.

### Deliverables

- Data map
- Privacy controls in-app
- Public policy docs

## Phase 5: Beta Program (Weeks 6-9)

### Objective
Validate product quality and value with real users.

### Actions

1. Build TestFlight cohorts:
   - Cohort A: trusted internal testers
   - Cohort B: target users with diverse devices
2. Define bug triage bands (P0, P1, P2).
3. Collect qualitative feedback weekly.
4. Track quantitative thresholds:
   - Crash-free sessions
   - Time to first value
   - Repeat usage

### Deliverables

- Weekly beta report
- Prioritized bug and UX backlog
- Go/no-go recommendation

## Phase 6: Launch Execution (Weeks 9-12)

### Objective
Release with confidence and observability.

### Actions

1. Finalize App Store listing assets and copy.
2. Verify screenshots and metadata by locale.
3. Lock the launch candidate build.
4. Run the launch checklist (see template).
5. Publish the changelog and support channels.

### Deliverables

- App Store submission package
- Launch operations runbook
- Incident response owner list

### Release discipline

An app with a backend is several deploy surfaces, not one. The binary, the
backend and any web surface each release independently, and shipping one does
not ship the others. [SHIPPING_AND_OPERATIONS.md](SHIPPING_AND_OPERATIONS.md)
covers the branch model, the release sequence, post-release probes, and the
App Store rename that does not reset review.

## Phase 7: Post-Launch Improvement (Ongoing)

### Objective
Improve retention and outcomes through rapid weekly loops.

### Weekly System

1. Diagnose one bottleneck using event and session data.
2. Propose one focused experiment.
3. Ship one meaningful change.
4. Measure impact after seven days.
5. Keep, iterate, or roll back.

Track decisions in [../templates/EXPERIMENT_LOG_TEMPLATE.md](../templates/EXPERIMENT_LOG_TEMPLATE.md).

### Monitoring

Build cheap, independent monitors. Do not wait to hear about problems from users
or from a provider's dashboard. The patterns (free provider APIs, scheduled
jobs, alert only on failure) are in
[SHIPPING_AND_OPERATIONS.md](SHIPPING_AND_OPERATIONS.md).

## Quality Gates

### Gate 1: Build Gate

- Core flows complete
- No blocker crashes
- Data model stable

### Gate 2: Beta Gate

- Positive signal on value comprehension
- Stable performance on supported devices
- P0 issues resolved

### Gate 3: Launch Gate

- Privacy and policy complete
- Support and monitoring active
- App Store assets approved

## Common Failure Modes

1. Too-broad initial scope
2. Building advanced features before a stable app shell
3. Late privacy work causing launch delays
4. Missing event taxonomy, making decisions subjective
5. No weekly experiment cadence post-launch
6. Assuming one deploy ships everything; backend, web and app are separate surfaces
7. Secrets that do not propagate: the same secret lives in several stores and a redeploy silently drops one
8. A feature PR merged into the release branch instead of the integration branch, silently forking them
9. A scheduled job installed under `~/Documents` on macOS, which the system blocks from running
10. A connected hosting project with a wrong build command that fails every PR, and gets ignored as noise

## Practical Rule

When uncertain, prioritize reliability and clarity over feature count.
A smaller app that users trust and return to beats a larger app that feels unstable.
