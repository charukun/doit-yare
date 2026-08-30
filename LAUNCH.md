# Launch

## Goal

Develop the MVP in five days or less with AI-assisted implementation. This is a development target, not a guaranteed public release date; Apple and Google review timelines determine actual availability.

Promotion runs in parallel with development.

## Day 1 — Specification, UX, and skeleton

- freeze Product, UX, Architecture, and Launch baselines;
- resolve blocking Open Questions;
- establish monorepo and shared domain boundaries;
- implement the one-screen state skeleton without expanding MVP scope;
- prepare localized minimal copy and product identifiers.

## Day 2 — iOS and Android payments

- integrate StoreKit consumable products;
- integrate Google Play Billing consumable one-time products;
- implement purchase started/completed states and idempotency;
- begin platform receipt/purchase verification and reconciliation;
- validate ×1 / ×10 / ×100 presentation and localized prices.

## Day 3 — Notification, completion, and repurchase loop

- request and handle notification permission;
- schedule the local notification;
- implement notification-opened behavior;
- require explicit completion;
- show “よくやった” acknowledgement;
- prefill and purchase the next occurrence;
- instrument the full core funnel.

## Day 4 — Backend, analytics, and supporting work

- implement minimum AWS Serverless API and infrastructure;
- persist purchase, scheduled time, and completion state;
- complete purchase/server/local-notification reconciliation paths;
- configure operational monitoring;
- validate product analytics events and KPI queries;
- advance Web/PWA only if it does not endanger mobile submission.

## Day 5 — Device QA and store submission

- run real-device tests on supported iOS and Android versions;
- test purchase sandbox flows for every multiplier;
- test denied notification permission and scheduling failures;
- test time, timezone, restart, offline, retry, and duplicate-action edge cases;
- verify accessibility and localization basics;
- prepare screenshots, descriptions, privacy disclosures, and reviewer notes;
- submit to Apple App Store and Google Play review.

## QA release gate

- Each purchase clearly applies to one displayed date/time and multiplier.
- No credit, balance, bundle, or subscription appears.
- Purchase success schedules exactly one intended notification.
- Opening a notification does not complete the action.
- Completion produces acknowledgement and a prefilled next purchase.
- Analytics do not double-count retries or reopened states.
- Refund, cancellation, pending-payment, and scheduling-failure states are understandable.
- DO NOT ADD features are absent.

## Promotion

Primary candidates:

- TikTok
- Instagram Reels
- YouTube Shorts
- Apple Ads
- Google App Campaigns
- Reddit
- X

Creative should demonstrate the experience rather than enumerate features:

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

Positioning:

- “a commitment device using real money”
- “buy one command to your future self at a time”

## Launch KPIs

- first-purchase conversion rate
- notification-to-completion rate
- completion-to-next-purchase rate
- seven-day repeat-purchase rate
- ×1 / ×10 / ×100 mix

The primary launch signal is:

`completion_pressed → next_purchase_completed`

Track technical guardrails alongside product KPIs:

- payment verification failures
- purchase-to-notification scheduling failures
- duplicate purchase/commitment incidents
- crash-free sessions
- store review rejection reasons

## Open Questions

- Which Open Questions must be resolved before Day 1 implementation begins, and who owns each decision?
- Are Apple/Google product identifiers and localized price tiers approved before Day 2?
- What minimum supported OS/device matrix is used for Day 5 QA?
- Is Web/PWA explicitly excluded from the five-day submission path unless mobile is already stable?
- Which analytics platform and attribution setup must be ready before paid acquisition?
- What initial acquisition budget and per-channel stop/scale rules apply?
- What KPI thresholds define a successful first cohort?
- Which countries/languages are included in the first submission?
- What is the response plan for store rejection or consumable-IAP policy questions?
- How are customer support, refunds, and failed-notification reports handled at launch?
