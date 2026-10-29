# Start Here

Use this guide as your operating system for shipping an iOS app from zero to
launch. It covers scope, PRD, skeleton, core loop, trust and safety, and App
Store submission. Read [IOS_APP_PLAYBOOK.md](IOS_APP_PLAYBOOK.md) for the full
phase framework and [SHIPPING_AND_OPERATIONS.md](SHIPPING_AND_OPERATIONS.md)
before your first release.

## 1) Pick Your Delivery Track

- 8-week track: MVP with one clear core loop
- 12-week track: full first release with onboarding, analytics and stronger quality controls
- 16-week track: first release plus growth loops, retention systems and deeper experimentation

If you are a solo builder, default to 12 weeks.

## 2) Define Your Single Core Job

Before writing code, complete these statements in one page:

- User: who exactly is this for?
- Job: what result do they want?
- Moment: when do they need your app most?
- Outcome: what measurable change proves your app works?

Output: a first PRD draft using [templates/PRD_TEMPLATE.md](../templates/PRD_TEMPLATE.md).

## 3) Build the App Skeleton First

Do this before any advanced features:

- App shell (navigation, auth state, settings)
- Data model and persistence strategy
- Event schema for analytics
- Error states and loading patterns
- Basic test harness

Output: a stable app shell that can host any feature set.

## 4) Implement the Core Loop

Your core loop is the shortest path from app open to user value.

Example loop shape:

1. Capture input
2. Process input
3. Return useful feedback
4. Save result
5. Prompt next action

Output: one polished loop that works reliably on real devices.

## 5) Add Trust and Safety Layers Early

Do not postpone this work:

- Permissions copy
- Data retention policy
- Delete and export controls
- Plain-language explanation of any automated decision the app makes
- Fallback behavior when the app is unsure

Output: a trust posture that users and reviewers can understand.

## 6) Ship in Controlled Releases

Use a staged rollout:

- Internal testing
- TestFlight cohort 1 (small)
- TestFlight cohort 2 (expanded)
- Public App Store launch

At each stage, review crash-free rate, task completion and retention signals.

## 7) Run Weekly Improvement Cycles

Every week:

1. Review metrics and user feedback
2. Identify one bottleneck in the core loop
3. Ship one high-confidence improvement
4. Log what changed and why

Use [templates/EXPERIMENT_LOG_TEMPLATE.md](../templates/EXPERIMENT_LOG_TEMPLATE.md).

## 8) Launch Readiness Gate

Before public release, complete [templates/APP_STORE_LAUNCH_CHECKLIST.md](../templates/APP_STORE_LAUNCH_CHECKLIST.md).

If any P0 item is incomplete, delay launch.

## Recommended Reading Order

1. [IOS_APP_PLAYBOOK.md](IOS_APP_PLAYBOOK.md)
2. [SHIPPING_AND_OPERATIONS.md](SHIPPING_AND_OPERATIONS.md)
3. [01-product-thesis-and-scope.md](../content-pieces/01-product-thesis-and-scope.md)
4. [02-app-architecture-for-small-teams.md](../content-pieces/02-app-architecture-for-small-teams.md)
5. [03-delivery-plan-and-sprint-system.md](../content-pieces/03-delivery-plan-and-sprint-system.md)
6. [04-privacy-trust-and-app-store-launch.md](../content-pieces/04-privacy-trust-and-app-store-launch.md)
