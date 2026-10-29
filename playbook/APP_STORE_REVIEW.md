# App Store Review: Getting Through the First Time

App Review is predictable once you treat it as a checklist, not a judgment. Most
rejections come from a small set of causes, and almost all of them are visible in
your own build before you submit. Guideline numbers below refer to the
[App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/);
the text changes several times a year, so read the current version before each
major submission.

---

## 1. Before you submit

- **Review the build as a reviewer would.** Fresh install, no cached state, on
  the oldest supported device. Every screen reachable, no placeholder text, no
  dead buttons, no "coming soon".
- **Give them a way in.** If the app needs an account, provide a working demo
  account in App Review Information, with any one-time codes disabled or fixed
  for that account. A reviewer who cannot sign in rejects for incompleteness (2.1).
- **Write the review notes.** Two or three short paragraphs: what the app does,
  how to reach the features that need explaining, why each sensitive permission
  is needed, and anything that needs hardware or a location (attach a short video
  if the reviewer cannot reproduce it at a desk).
- **Match the metadata to the build.** Screenshots from the real app, on the
  required device sizes, showing the current version. A description that promises
  a feature the build does not have is a metadata rejection (2.3).
- **Fill the privacy questionnaire from your data map, not from memory.** Every
  data type the app or its SDKs collect, and whether it is linked to the user or
  used for tracking. Third-party SDKs count. Mismatches between the questionnaire,
  the privacy manifest and the app's behavior are a common rejection (5.1.1, 5.1.2).
- **Use TestFlight first.** Internal testers need no Beta review; external
  testers do, and that Beta review surfaces most problems a week earlier.

## 2. The rules that surprise people

- **Account deletion is mandatory if accounts exist (5.1.1(v)).** A visible path
  in the app, not only a support email, and it must delete the account and its
  data, not just sign out. A support form is acceptable only for legal or
  regulated cases, and you must explain why.
- **Sign in with Apple (4.8).** If you offer any third-party sign-in (Google,
  Facebook and so on), you must also offer Sign in with Apple or an equivalent
  privacy-preserving option. Passkeys and email links alone do not trigger it.
- **Permission strings must be specific (5.1.1(i)).** Each usage description
  says what the app does with the data and why, in one active sentence. A generic
  "needed for a better experience" is rejected. Request at the moment of need,
  not at launch.
- **Digital goods go through In-App Purchase (3.1.1).** Unlocking features,
  subscriptions and digital content inside the app must use IAP. Physical goods
  and services consumed outside the app (a ticket, a delivery, a booking) must
  not use IAP (3.1.3(e)) and may use your own payment processor.
- **Linking out to web purchases.** The rules differ by storefront and have
  changed through court orders and regulation. Read the current guideline text for
  your storefronts before relying on an external purchase link, and do not assume
  what applied last year.
- **Login should not be required to browse (5.1.1(ii)).** If the app's core
  content does not depend on an account, let people see it before signing up.
- **Minimum functionality (4.2).** A thin wrapper around a website, or an app
  that is mostly marketing, is rejected. The app must do something native.
- **Regulated categories need a legal entity (5.1.1(ix), 3.2.1(viii)).** Apps
  that handle money, health or other regulated services must be submitted by the
  legal entity that provides the service, not an individual developer account.
- **Public APIs only (2.5.1).** Private frameworks, undocumented selectors and
  runtime tricks fail the automated scan before a human sees the build.
- **No review-prompt gaming (1.1.7, 5.6).** Use the system review prompt, never
  a custom "rate us" dialog, and never offer anything for a rating.

## 3. Submission mechanics

- **Version and build.** Bump the marketing version for every store release;
  the build number must be unique per upload. Review a release candidate you have
  already run on a device from TestFlight.
- **Phased release.** Turn it on for anything with a backend. It rolls the update
  out over seven days to automatic-update users, and you can pause it if your
  monitoring shows a problem.
- **Hold for developer release.** Approve now, release when you choose. Use it to
  line up the app release with the backend deploy, since the two are separate
  surfaces.
- **Expedited review** exists for a critical fix or a time-bound event. Ask
  through App Store Connect, state the reason plainly, and do not use it twice in
  a row.
- **Renaming without a review reset.** Change the App Store name and
  `CFBundleDisplayName` on the existing record. A new bundle identifier means a new
  record and a full first-submission review. See
  [SHIPPING_AND_OPERATIONS.md](SHIPPING_AND_OPERATIONS.md).

## 4. When you are rejected

- **Read the exact guideline cited and reply in Resolution Center first.** Many
  rejections are a misunderstanding the reviewer can clear in a day if you explain
  where the feature is or why the rule does not apply. Do not resubmit silently.
- **Fix what is actually wrong, then resubmit with notes.** State what changed
  and where to find it. The next reviewer may not be the same person.
- **Appeal when the guideline does not apply.** The App Review Board exists for
  that. Keep it factual and quote the guideline text.
- **Keep a rejection log.** Date, guideline, what the reviewer saw, what fixed
  it. Over a few releases this becomes your own pre-submission checklist.

## 5. The two-week rule

Plan the first submission at least two weeks before any date that matters. A
first review usually takes a day or two; a rejection and resubmission add a few
more; a second rejection on a design or business-model point can take longer.
Nothing about launch day should depend on review finishing on a particular day.
