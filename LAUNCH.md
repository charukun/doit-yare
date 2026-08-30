# LAUNCH

## 1. Launch objective

Build the core paid commitment loop in **five development days or less**, then let App Store / Google Play review timing determine the actual public release date.

This is not a six-week waterfall plan. AI-assisted implementation is assumed throughout development, testing, documentation, and store preparation.

The goal is speed without changing the core product thesis.

## 2. What counts as launch-ready MVP

The mobile MVP is launch-ready when a real user can reliably complete this loop on iOS and Android:

```text
see concrete future date/time
→ choose ×1 / ×10 / ×100
→ make a real one-time purchase
→ notification is scheduled
→ receive/open “やれ！”
→ perform the action
→ explicitly press “完了”
→ see “よくやった”
→ see next commitment prefilled
→ optionally buy the next commitment with a new payment
```

And when the team can measure:

- first purchase,
- notification open,
- completion,
- next purchase,
- multiplier mix.

## 3. Five-day build target

### Day 1 — Product contract, UX, and skeleton

Goals:

- freeze `PRODUCT.md`, `UX.md`, `ARCHITECTURE.md`, `LAUNCH.md`,
- resolve only implementation-blocking Open Questions,
- initialize monorepo,
- create mobile one-screen state skeleton,
- define domain states and analytics event contract,
- establish iOS/Android build pipelines,
- prepare store product identifiers and sandbox/test configuration.

Exit condition:

The full core loop is visible as mocked state transitions without adding non-MVP features.

### Day 2 — iOS / Android payments

Goals:

- configure `commit_x1`, `commit_x10`, `commit_x100`,
- integrate StoreKit consumable purchase path,
- integrate Google Play Billing consumable one-time purchase path,
- implement purchase cancellation/failure handling,
- add server-side transaction verification/reconciliation baseline,
- emit `purchase_started` and verified `purchase_completed`.

Exit condition:

Sandbox/test purchases create exactly one commitment without credits or subscriptions.

### Day 3 — Notification, completion, next purchase

Goals:

- local notification permission flow,
- schedule the localized “やれ！” notification,
- handle notification-open routing,
- implement due screen,
- implement explicit completion,
- show “よくやった”,
- prefill next commitment,
- complete fresh next-purchase loop,
- emit notification/completion/next-purchase analytics events.

Exit condition:

The defining end-to-end loop works on physical iOS and Android devices.

### Day 4 — Backend, analytics, resilience, web baseline

Goals:

- harden AWS serverless persistence,
- idempotency and retry paths,
- CloudWatch logs/alerts for paid-flow failures,
- analytics verification,
- account/device synchronization decision implemented as required,
- begin Next.js/Web/PWA only after mobile critical path is stable,
- prepare privacy/terms/store metadata drafts.

Exit condition:

Paid state transitions survive realistic retry/restart cases, and KPI events are queryable.

### Day 5 — Device testing and store submission

Goals:

- real-device regression on supported iOS/Android versions,
- timezone/date-boundary testing,
- payment cancellation/retry testing,
- notification permission testing,
- offline/interruption cases defined by MVP policy,
- accessibility smoke test,
- localized copy/price review,
- store screenshots/video assets,
- App Store / Google Play listing metadata,
- privacy declarations,
- submit builds for review.

Exit condition:

Both store submissions are in review or otherwise awaiting store-side processing, with no known blocker in the core paid loop.

## 4. Parallel workstreams

Development and launch preparation should overlap.

### Product / engineering

- payment product setup
- mobile implementation
- backend
- analytics
- testing

### Store operations

- app records
- product configuration
- sandbox/test accounts
- privacy declarations
- screenshots
- descriptions
- review notes

### Marketing

- short-form creative production
- landing/store creative
- channel setup
- small-budget creative tests where allowed before/at launch

Do not wait until Day 5 to begin store or creative work.

## 5. Marketing position

Do not position this primarily as another habit tracker.

Lead with the unusual mechanism:

- a real-money commitment device,
- buy one command to your future self,
- each “やれ！” costs real money because that is the point.

Potential concise concepts:

- “未来の自分への命令を、1回ずつ買う。”
- “¥100払って、明日の自分に『やれ！』を送る。”
- “Habit trackerではなく、เงินจริงのCommitment Device。”

Final ad copy can be tested, but it must not imply that payment is a wallet, bet, prize pool, or subscription if the product is not one.

## 6. Creative format

Show the experience rather than explaining features.

Example short-form sequence:

```text
明日 07:00
¥100
↓
決済
↓
翌朝
「やれ！」
↓
行動
↓
完了
↓
「よくやった」
↓
明日分 ¥100
```

A 10–20 second vertical video can communicate the thesis more clearly than a feature checklist.

Useful creative variations:

- wake up / workout scenario without storing a “workout” habit in product UI,
- desk / study scenario,
- cleaning scenario,
- anonymous screen-only product demo,
- ×1 / ×10 / ×100 commitment-intensity contrast.

The ads may depict a use case. The product itself still does not need habit names/categories.

## 7. Candidate acquisition channels

Initial candidates:

- TikTok
- Instagram Reels
- YouTube Shorts
- Apple Ads
- Google App Campaigns
- Reddit
- X

Channel priority should be determined by actual creative performance and cost, not by adding channel-specific product features.

## 8. Launch analytics

### Funnel

```text
app_opened
→ purchase_started
→ purchase_completed
→ notification_scheduled
→ notification_opened
→ completion_pressed
→ next_purchase_completed
```

### Primary launch KPI

The most important behavioral transition is:

`completion_pressed → next_purchase_completed`

It directly tests whether a successful commitment creates willingness to make another paid commitment.

### Supporting KPIs

- first-purchase rate
- purchase-start → purchase-complete rate
- notification → completion rate
- 7-day repeat-purchase rate
- multiplier mix: ×1 / ×10 / ×100
- paid-flow technical failure rate

## 9. Early experiment principles

Experiment with:

- ad hook,
- demo scenario,
- store creative,
- concise positioning language,
- localized price points where platform/store strategy requires it.

Do not “optimize conversion” by violating product principles.

Examples of invalid launch experiments:

- subscription trial,
- free credit pack,
- bonus commands,
- “buy 10, get 2 free”,
- recommended/highlighted ×10 multiplier,
- streak rewards to increase retention.

Those would change the product being validated.

## 10. Store review preparation

Because the app intentionally sells consumable digital purchases, store-review notes should clearly explain:

- each IAP purchases one commitment event,
- the products are consumable,
- there is no wallet/subscription,
- the user selects a future notification time,
- completion is a user action after the real-world activity,
- Apple Watch / Wear OS behavior in MVP is notification mirroring rather than a dedicated watch app.

Exact store policy wording and IAP configuration should be checked against current Apple/Google documentation immediately before implementation/submission.

## 11. QA matrix

At minimum test:

### Purchase

- ×1 success
- ×10 success
- ×100 success
- user cancels store sheet
- store returns pending/failure where applicable
- app is killed/interrupted during purchase
- duplicate/retry transaction handling

### Scheduling

- notification permission granted
- notification permission denied
- app restart after scheduling
- device restart where platform behavior matters
- date boundary
- timezone change
- DST locale where relevant

### Completion

- notification opened
- app opened manually after due time
- completion pressed once
- repeated completion request/retry
- next default shown correctly
- next purchase recorded distinctly

### Analytics

Verify each required event occurs once per intended semantic action, with stable commitment/product identifiers.

## 12. Post-submission plan

While stores review:

- continue physical-device QA,
- fix only bugs or review blockers without expanding scope,
- finish launch creatives,
- prepare channel campaigns,
- validate landing/privacy/support pages,
- monitor store review feedback,
- keep the MVP feature boundary intact.

No PR merge or production rollout should bypass deliberate review just to preserve the five-day target.

## 13. Open Questions

1. Final app/store name and branding.
2. Supported OS minimum versions for first release.
3. Exact App Store / Google Play localized price points.
4. Store account/product configuration readiness and review-history constraints.
5. Identity/account model and required privacy disclosures.
6. Analytics provider and consent requirements by region.
7. Whether web/PWA work truly fits Day 4 or should remain a post-mobile task if it threatens store submission.
8. Customer support/refund handling channel for a paid-per-event product.
9. Initial launch countries/languages beyond Japan.
10. Initial marketing test budget and channel priority.
11. Whether the first release permits multiple concurrent commitments.
12. Notification permission failure policy before purchase.
