# IO22 iOS Playbook

A field guide for taking a native iOS app from a blank repo to the App Store, and
keeping it healthy afterwards, as a solo developer or a very small team. It is
the general method we use at IO22 Labs, written down so we stop re-deriving it.

It is deliberately generalized. It names no client, no product and no secret. It
describes patterns, not any one app's internals.

## What holds it together

- **Scope first.** Most apps fail on scope before engineering quality matters.
  One user, one job, one core loop, then everything else.
- **Automate anything you do twice.** Builds, releases, credential handling and
  monitoring are scripts, so every run is reproducible and the next one is cheaper.
- **Find out before your users do.** Crash reporting, health checks and alerts are
  wired in before the first release, not after the first incident.
- **Treat trust as load-bearing.** Privacy, permissions, data deletion and secret
  hygiene are built in from the first week, because they are what delays launches.

## Contents

### The playbook

| Document | What it covers |
|---|---|
| [playbook/START_HERE.md](playbook/START_HERE.md) | Pick a delivery timeline and the eight steps from idea to launch |
| [playbook/IOS_APP_PLAYBOOK.md](playbook/IOS_APP_PLAYBOOK.md) | The phase-by-phase execution framework: strategy, PRD, foundation, core loop, privacy, beta, launch, post-launch |
| [playbook/APP_STORE_REVIEW.md](playbook/APP_STORE_REVIEW.md) | Getting through App Review the first time: preparation, the rules that surprise people, submission mechanics, handling a rejection |
| [playbook/SHIPPING_AND_OPERATIONS.md](playbook/SHIPPING_AND_OPERATIONS.md) | General lessons on releasing and running an app: deploy surfaces, branches, secrets, scheduled jobs, monitoring, money basics, macOS gotchas |

### Short reads

- [content-pieces/01-product-thesis-and-scope.md](content-pieces/01-product-thesis-and-scope.md)
- [content-pieces/02-app-architecture-for-small-teams.md](content-pieces/02-app-architecture-for-small-teams.md)
- [content-pieces/03-delivery-plan-and-sprint-system.md](content-pieces/03-delivery-plan-and-sprint-system.md)
- [content-pieces/04-privacy-trust-and-app-store-launch.md](content-pieces/04-privacy-trust-and-app-store-launch.md)

### Templates

- [templates/PRD_TEMPLATE.md](templates/PRD_TEMPLATE.md), a one-page product definition
- [templates/EXPERIMENT_LOG_TEMPLATE.md](templates/EXPERIMENT_LOG_TEMPLATE.md), a decision log
- [templates/APP_STORE_LAUNCH_CHECKLIST.md](templates/APP_STORE_LAUNCH_CHECKLIST.md), a submission checklist

## How to use it

1. Read [START_HERE.md](playbook/START_HERE.md) and pick a track.
2. Draft the PRD from the template. Stand up the app skeleton before any feature.
3. Follow the phases in [IOS_APP_PLAYBOOK.md](playbook/IOS_APP_PLAYBOOK.md).
4. Before the first release, read [SHIPPING_AND_OPERATIONS.md](playbook/SHIPPING_AND_OPERATIONS.md)
   and set up releases, secrets and monitoring the way it describes.
5. Prepare the submission with [APP_STORE_REVIEW.md](playbook/APP_STORE_REVIEW.md).
6. Run the launch checklist. If any P0 item is open, delay.

## Contributing

Issues and pull requests that improve the general method are welcome. Please keep
contributions free of product-specific or client-specific material.

## License

[MIT](LICENSE). Copyright IO22 LLC.
