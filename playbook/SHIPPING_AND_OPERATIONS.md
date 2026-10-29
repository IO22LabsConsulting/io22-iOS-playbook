# Shipping and Operations: General Lessons

What we have learned releasing and running apps as a very small team. None of it
is specific to one product. Vendor names appear only where the behavior is the
vendor's; swap in your own.

The bias throughout is the command line. A step that is a CLI command can be
scripted, versioned and re-run. A dashboard click cannot.

---

## 1. An app is several deploy surfaces

An app with a backend ships from at least three places: the app binary (App
Store or TestFlight), the backend (functions, migrations, scheduled jobs), and
any web surface (storefront, admin, marketing). A backend deploy does not ship
the web. A web deploy does not ship the app. A change touching two surfaces is
not live until both deploy.

- Write down, per surface, the one command that releases it.
- After any release, probe every surface, including the ones you did not touch.
  Anything the deploy tool does not manage (a host's environment variables, a
  separate web project) needs its own post-deploy check. Assume nothing propagated.
- A release of "just the functions" still replaces every function. Approve a
  release on the source revision, not on the size of the plan.

## 2. Branches and promotion

A simple model that holds up:

- One integration branch (for example `development`) and one release branch
  (for example `main`).
- Features branch off integration and merge back by pull request, squash-merged.
- The release branch only ever receives the integration branch, through a
  promotion pull request merged as a **merge commit**. A squash re-forks the two
  branches.
- Production deploys from a version tag on the release branch.

Two checks that prevent the common accident:

- Before promoting, confirm the release branch is not ahead of integration:
  `git rev-list --count --no-merges origin/development..origin/main` must be `0`.
- Add a required status check that fails any pull request into the release
  branch whose head is not the integration branch. Branch protection alone
  cannot express a source-branch rule, and one mistargeted PR silently forks the
  branches.

Keep the merge gate local and honest: run the full test commands on your own
machine, and paste the real output into the PR. A claim is not evidence.

## 3. The App Store rename that does not reset review

To ship under a new name or brand, change the **App Store name and
`CFBundleDisplayName`** on the **existing** app record. It rides a normal version
update.

Changing the **bundle identifier** creates a brand-new app record: a full
first-submission review, plus redoing provisioning, TestFlight groups and all
listing metadata.

Do not rename the Xcode project or module just to change the user-facing name;
that is invisible churn. Keep internal Keychain service identifiers unchanged;
renaming them signs out every installed device.

## 4. Secrets

The rule that saves the most grief: **a real app has the same secret in several
places, and knowing which store feeds which tool is half the battle.**

| Store | Holds | Read by |
|---|---|---|
| Password vault | the source of truth for every secret | humans, and setup scripts through the vault's CLI |
| macOS login Keychain | a local cache for CLI tools and unattended jobs | the `security` CLI, scheduled jobs |
| Platform environment | the runtime copy the deployed code reads | your functions host, your web host |

Rules:

- Never put a secret on the command line as a literal. It lands in the process
  table and shell history. Read it from the vault into a variable, or into a
  `mktemp` file you `chmod 600` and delete.
- Never hand-paste a secret into a dashboard. A script reads the vault item and
  pushes it to the platform CLI. Rotation then becomes: update the vault item,
  re-run the script, redeploy.
- Name vault items by role, not value, and keep one config file that maps
  `ENV_VAR | environment | vault-item-name`. Nobody memorizes names.
- Gate on key shape in code. A live key must be refused in a test environment
  and a test key refused in production. A wrong-mode key is a silent money bug.
- Unattended jobs cannot type a vault master password, and you must never store
  one to make the vault unattended. Copy the secret from the vault into the login
  Keychain once with a setup script (`security add-generic-password -U ...`); the
  job reads it with `security find-generic-password -s <service> -w`. The vault
  stays the source of truth; re-run the setup after a rotation.
- Run a secret scanner in CI so a committed secret fails the build.
- Shell trap: `bw get password <NAME>` with angle-bracket placeholders makes zsh
  try to read a file. Substitute the real name or use a variable.
- "Is it actually set?" trap: a secret set from an empty variable has the SHA-256
  of the empty string (`e3b0c442...`). If a platform lists secret digests, that
  digest means you set it empty.

## 5. Scheduled jobs on macOS (`launchd`)

A Mac you already own is a free scheduler for monitors and reports. Two gotchas
cost real hours:

- Install the scripts **outside `~/Documents`**. That folder is protected by
  macOS privacy controls, and a `launchd` agent trying to execute there fails with
  "Operation not permitted" (exit 126). Put them under
  `~/Library/Application Support/<your-tool>/`.
- Use the modern `launchctl bootstrap gui/$(id -u) <plist>` and
  `launchctl kickstart gui/$(id -u)/<label>`. The older `load` and `start` are
  silent no-ops on recent macOS.

Feed the job's secrets from the login Keychain (section 4), and have the wrapper
email you only on a non-zero exit. Manual checks become standing daily alerts.

## 6. Monitoring

Three layers, cheapest first:

1. **In the code.** A crash reporter on every runtime, wired so it starts only
   when a DSN is present and scrubs personal data, request bodies and key material
   before sending. Shipping without a DSN is then truly a no-op, and turning it on
   is configuration, not a release.
2. **Provider-native alerts.** The notification toggles every provider offers
   (failed webhook endpoint, dispute opened, cron failure, database disk). Free,
   and flipped once.
3. **Independent scripted monitors.** The layer most teams skip. A small script
   that reads the provider's **free REST API** and exits non-zero past a threshold.
   You usually do not need the provider's paid analytics add-on for this.

Watch for the failure you cannot see from your own database. A payment that fails
before your webhook fires has no row on your side; only the provider's API knows.
Monitor those from the provider.

## 7. Money basics, if your app takes payments

These are the non-negotiables. They are textbook, and they are still the ones
that get skipped.

- Money is integer minor units (cents) or fixed decimal, never a float.
  Percentage math uses one closed form, pinned with golden test vectors.
- Never trust a client-supplied amount. Recompute every total server-side from
  stored prices. If the recomputed total disagrees with what was charged, record
  the charged amount and raise an alert; never silently absorb the mismatch.
- Idempotency is a unique constraint, not application logic. A client-minted key
  on the row makes a replayed request collide and read the original back. Pass
  the same key to the payment provider.
- Webhook handlers verify signatures, dedupe on the provider's object id, and
  return an error (so the provider retries) if any part of the write fails.
- A receipt or entitlement exists only after a verified capture, never on an
  authorized-but-uncaptured intent.
- Snapshot what was sold (name, price, code) on the order so a later rename does
  not rewrite history.
- Reconcile on a schedule. Compare the provider's settled transactions to your
  orders and surface differences daily until closed.
- Let the provider's SDK own card data. You should never see a card number, and
  logging a payment payload undoes that.

## 8. Database habits that pay off later

- Schema evolution is additive. New nullable columns, never a breaking rename,
  because old app versions stay installed. Decode unknown enum values leniently
  and fail closed.
- Soft-delete, but filter every read path. Centralize the "what is live"
  predicate in one helper, or deleted rows resurrect in the one picker nobody
  updated.
- Multi-row writes that must succeed together go in one transaction.
- Swift encodes UUIDs uppercase and Postgres stores them lowercase. Compare
  case-insensitively on the server, or joins silently miss.
- Never edit an applied migration. Write a new one. The schema is then
  reproducible from the files, and correcting forward always works.

## 9. Field notes

- **macOS privacy controls and `~/Documents`.** A working tree under
  `~/Documents` can make development tooling lose file access ("Operation not
  permitted", `EPERM`). Grant the tool Documents access, or reset with
  `tccutil reset SystemPolicyDocumentsFolder <bundle-id>` and relaunch; a running
  process caches the denial. This recurs after every tool update, because a new
  binary is a new subject.
- **Device pairing on a remote build Mac.** A headless Mac often pairs devices
  over the network, not USB. After an iOS update a previously working device shows
  as unavailable: re-trust it and re-enable Developer Mode. A charge-only cable
  shows nothing on the USB bus at all.
- **Hosting projects that build the same repo.** Two projects on the same
  repository double-fire any cron defined in the repo, and a misconfigured one
  fails every PR preview. Guard cron routes with a secret, and disconnect the Git
  integration on projects you deploy from the CLI.
- **Payment providers have a minimum charge.** A two-cent test charge is
  rejected; use at least the provider's minimum.
- **Two credential systems on a managed database.** The database password (for
  the CLI and migrations) is separate from the API keys the app uses. Resetting
  the first is safe for the running app; rotating the second is not.

## 10. The habit behind all of it

If you do it twice, script it. Every dashboard-only workflow you convert to a
command (release, secret rotation, monitoring, environment setup) is cost removed
from every future run. Pay for the hard, risky infrastructure. Automate the
boring, reproducible parts.
