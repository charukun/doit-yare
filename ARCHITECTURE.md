# Architecture

## Status

This is the proposed pre-implementation baseline. Technology choices and unresolved consistency rules must be confirmed before application code starts.

## Monorepo

Proposed structure:

```text
apps/
  mobile/
  web/

packages/
  domain/
# ARCHITECTURE

## 1. Architecture goals

The MVP architecture should optimize for:

- shipping iOS and Android quickly,
- keeping the product model small,
- reliable per-event purchase verification,
- reliable local notification scheduling,
- server-side record of purchase / scheduled time / completion,
- clean analytics for the core loop,
- a path to web/PWA without forcing web complexity into the mobile MVP.

Avoid architecture that creates product features not present in `PRODUCT.md`.

## 2. Proposed monorepo

```text
apps/
  mobile/
  web/

packages/
  domain/
  ui/
  i18n/

backend/
  api/
  infrastructure/
```

### `apps/mobile`

React Native + TypeScript first candidate for iOS and Android.

Responsibilities:

- one-screen state UI,
- date/time selection,
- multiplier selection,
- StoreKit / Google Play Billing integration,
- local notification scheduling,
- explicit completion action,
- local state/cache,
- analytics emission,
- API synchronization.

### `apps/web`

Next.js + TypeScript, implemented after mobile core is complete.

Responsibilities:

- equivalent commitment loop for web/PWA,
- per-event web payment,
- web notification behavior only if it can meet the product promise clearly.

Do not use web credits to emulate mobile consumables.

### `packages/domain`

Pure shared domain model where sharing remains practical.

Candidate concepts:

- commitment state,
- money/product multiplier identifiers,
- schedule value object,
- transition rules,
- analytics event names.

This package should not become a generic framework.

### `packages/ui`

Only genuinely shared presentational primitives.

Do not over-invest in a design system before the single-screen flow is validated.

### `packages/i18n`

Localized strings and locale-aware formatting for:

- command copy,
- completion copy,
- purchase UI,
- dates/times,
- currency display,
- error/system copy.

### `backend/api`

Serverless handlers for the minimal backend contract.

### `backend/infrastructure`

Infrastructure as code for AWS resources, environments, observability, and deployment.

## 3. Mobile stack baseline

First candidate:

- React Native
- TypeScript

The exact React Native framework/build setup should be selected at implementation time based on current StoreKit/Play Billing/local-notification support and the fastest reliable path to store submission.

Do not lock into a framework wrapper if it makes consumable IAP verification or notification scheduling less reliable.

## 4. Backend baseline

AWS Serverless first candidate:

- API Gateway
- Lambda
- DynamoDB
- CloudWatch
- EventBridge-family services only where they solve a concrete backend need

The mobile notification itself should primarily be a **local notification**, while the server stores the purchased commitment and its state.

This avoids making ordinary due-time delivery dependent on a push pipeline while preserving backend truth for purchase/completion/account synchronization.

## 5. Domain model

Keep the model intentionally small.

### Commitment

Candidate fields:

```text
id
user_or_device_id        // exact identity model TBD
platform                 // ios | android | web
product_id               // commit_x1 | commit_x10 | commit_x100
amount
currency
scheduled_at
scheduled_timezone_meta  // exact semantics TBD
purchase_provider
purchase_reference
purchase_verified_at
notification_scheduled_at
notification_opened_at
completed_at
created_at
updated_at
```

Fields are implementation candidates, not permission to expose new UI concepts.

### Candidate commitment states

```text
PURCHASE_PENDING
PURCHASED
SCHEDULED
DUE
COMPLETED
```

Additional technical states may exist for verification failures/refunds, but they should not leak into the primary UX unless necessary.

## 6. State transition rules

Conceptually:

```text
Ready
  └─ purchase started
       ├─ cancelled/failed → Ready
       └─ verified success → Purchased
                              └─ local notification scheduled → Scheduled

Scheduled
  └─ due/opened → Due
                  └─ explicit completion press → Completed
                                                └─ next purchase begins → new commitment
```

Important invariant:

**Notification delivery/open must never cause the `COMPLETED` transition.**

Only the explicit completion action does that.

## 7. Payments

### iOS

Use StoreKit consumable in-app purchases.

Products:

- `commit_x1`
- `commit_x10`
- `commit_x100`

### Android

Use Google Play Billing consumable one-time products.

Products:

- `commit_x1`
- `commit_x10`
- `commit_x100`

### Core payment invariant

One successful product purchase authorizes exactly one new commitment purchase event under the agreed server reconciliation model.

Do not build:

- internal wallet balance,
- credit ledger exposed as spendable units,
- subscription entitlement,
- bundle inventory of future commands.

### Verification

Recommended MVP direction:

1. mobile initiates store purchase,
2. store returns transaction/purchase token,
3. backend verifies/reconciles the store purchase using the platform-supported server mechanism,
4. backend records the commitment,
5. client schedules local notification,
6. client/server record scheduling success.

The exact StoreKit / Play verification APIs must be confirmed against current platform guidance at implementation time.

## 8. Notification architecture

### Primary path

Use phone local notifications for the scheduled `やれ！` / localized equivalent.

Benefits:

- no dependency on server push delivery at the exact moment,
- direct mapping from a purchased commitment to an on-device scheduled notification,
- native mirroring to Apple Watch / Wear OS where enabled.

### Backend record

Backend stores at least:

- purchase identity,
- scheduled time,
- notification scheduling status/time,
- notification open event where observable,
- completion state.

### Reliability invariant

The app should not claim a commitment is successfully scheduled until the notification has been successfully scheduled under the platform APIs and the state has been recorded consistently enough for recovery.

Exact ordering between backend commit creation and local scheduling requires an idempotent recovery design.

## 9. Idempotency and recovery

Payments and mobile lifecycle transitions can be interrupted. The MVP should handle retry without creating multiple commitments from one transaction.

Use stable purchase/provider transaction identifiers as part of idempotent server processing.

Candidate invariants:

- one store transaction → at most one commitment,
- retrying verification does not duplicate a commitment,
- retrying completion does not produce multiple completion transitions,
- analytics retries use event IDs or equivalent deduplication where necessary.

## 10. API surface

Keep API scope narrow. Candidate endpoints/actions:

```text
POST /purchases/verify-and-create-commitment
GET  /commitment/current
POST /commitments/{id}/notification-scheduled
POST /commitments/{id}/notification-opened
POST /commitments/{id}/complete
```

Actual endpoint shapes may differ, especially after the one-active-vs-multiple decision.

Do not add generic habit/task CRUD APIs because the product has no habit/task object in MVP.

## 11. Data store

DynamoDB is the first candidate.

Design should support the actual access patterns, likely:

- fetch current commitment for identity,
- look up commitment by ID,
- enforce purchase transaction idempotency,
- record state transitions,
- derive minimal KPI data or forward analytics events.

Do not create a large relational domain model for nonexistent habit features.

## 12. Analytics

Required event contract:

- `app_opened`
- `purchase_started`
- `purchase_completed`
- `notification_scheduled`
- `notification_opened`
- `completion_pressed`
- `next_purchase_completed`

Recommended common properties, where privacy-appropriate:

```text
platform
app_version
locale
commitment_id
product_id
multiplier
amount
currency
is_next_purchase
```

Do not collect custom habit/task content because none exists.

Analytics provider is not yet selected.

## 13. Observability

CloudWatch baseline:

- Lambda errors
- API latency/error rates
- purchase verification failures
- duplicate/idempotency conflicts
- commitment creation failures
- completion persistence failures

The product is paid per event, so purchase-success-but-commitment-failure deserves especially visible alerting/logging.

## 14. Security and integrity

Minimum architecture expectations:

- never trust client-supplied price as proof of purchase,
- verify store transaction identity server-side,
- use authenticated/authorized API calls under the chosen identity model,
- make completion idempotent,
- avoid logging full payment credentials or unnecessary personal data,
- keep environment secrets out of the repository,
- separate development/staging/production store/backend configuration.

## 15. Web architecture

Web/PWA comes after the mobile loop.

Requirements that must carry over:

- every commitment is a separate real-money transaction,
- no wallet/credit abstraction,
- explicit completion,
- minimal single-flow UX,
- same analytics semantics where meaningful.

Web payment provider and notification strategy remain open.

## 16. MVP implementation order

Architecture should support this sequence rather than forcing layer-by-layer waterfall work:

1. domain/state skeleton + one-screen mobile shell,
2. iOS/Android consumable purchase path,
3. local notification scheduling,
4. completion + next-purchase loop,
5. backend persistence/verification/recovery,
6. analytics/observability,
7. web/PWA baseline after mobile path is stable,
8. store hardening and submission.

This ordering matches the five-day launch target while keeping the paid core loop testable early.

## 17. Open Questions

1. **Identity/authentication:** device-only vs account-based identity is not defined.
2. **One vs multiple active commitments:** this changes API/data access patterns and possibly the screen model.
3. **Authoritative purchase flow:** exact timing of server verification relative to local scheduling needs a failure-safe transaction/recovery design.
4. **Store server APIs:** choose the current StoreKit server verification / App Store Server Notifications and Google Play Developer API/RTDN strategy during implementation based on current platform guidance.
5. **Refund/revocation:** define resulting commitment state and whether a scheduled local notification is cancelled.
6. **Notification permission gate:** decide whether purchase is blocked until permission/scheduling capability is confirmed.
7. **Exact scheduling semantics:** absolute instant vs local wall-clock time during timezone/DST/device-clock changes.
8. **Offline behavior:** define what is allowed if payment succeeds but backend is temporarily unreachable, or if completion is pressed offline.
9. **Analytics provider:** provider not selected.
10. **Web payment provider:** provider not selected.
11. **React Native delivery stack:** choose the current fastest reliable build/tooling option after checking IAP + notification compatibility; do not pre-commit to a wrapper solely for convenience.
12. **Privacy/legal requirements:** final privacy policy, terms, analytics consent, and regional payment disclosures should be reviewed before store submission.
  ui/
  i18n/

backend/
  api/
  infrastructure/
```

- `apps/mobile`: iOS and Android client
- `apps/web`: later Web/PWA client
- `packages/domain`: shared domain types, rules, and state transitions
- `packages/ui`: reusable UI primitives where practical
- `packages/i18n`: minimal localized commands, acknowledgements, prices, and copy
- `backend/api`: serverless API handlers
- `backend/infrastructure`: infrastructure as code

## Clients

### Mobile

First candidate: React Native with TypeScript.

MVP priority is iOS and Android. Apple Watch and Wear OS initially rely on mirrored phone notifications; dedicated watch applications are post-MVP.

### Web

Next.js with TypeScript is the first candidate. Web/PWA work follows completion of the mobile experience and retains per-occurrence payment rather than credits.

## Backend

AWS Serverless is the first candidate:

- API Gateway
- Lambda
- DynamoDB
- CloudWatch
- EventBridge-family scheduling only where needed

The mobile client uses local notifications as the primary delivery mechanism. Server state also records purchases, scheduled notification times, and completion status to support consistency and analytics.

## Payments

### iOS

StoreKit consumable in-app purchases.

### Android

Google Play Billing consumable one-time products.

### Product concepts

- `commit_x1`
- `commit_x10`
- `commit_x100`

Each successful purchase funds one occurrence. Consumables must never surface to the user as credits or a prepaid balance.

### Web

Every occurrence is paid individually. No credit conversion or balance is introduced.

## Notifications

- Schedule local notifications on the mobile device after validated purchase completion.
- Store the intended notification instant and relevant timezone context on the server.
- Record notification scheduling and opening analytics.
- Define reconciliation for purchase success followed by scheduling failure.
- Define rescheduling behavior before allowing edits to a paid occurrence.

## Conceptual data model

### User or installation

- account/installation identifier
- locale
- timezone context
- notification-permission status as observed by client

### Commitment

- commitment identifier
- owner/account/installation identifier
- multiplier: `x1`, `x10`, or `x100`
- localized price and currency charged
- scheduled instant
- scheduling timezone/context
- state: proposed, purchase pending, purchased, scheduled, due/opened, completed, cancelled/refunded, or failed
- purchase reference
- created, purchased, opened, and completed timestamps

### Purchase

- platform
- platform transaction reference
- product identifier
- amount and currency
- verification/reconciliation status
- refund/cancellation status

### Analytics event

- event name
- timestamp
- commitment identifier when applicable
- platform/app version
- minimal non-sensitive properties required for KPI computation

Exact schemas and state transitions remain implementation decisions after the open questions are resolved.

## Analytics

Required events:

- `app_opened`
- `purchase_started`
- `purchase_completed`
- `notification_scheduled`
- `notification_opened`
- `completion_pressed`
- `next_purchase_completed`

CloudWatch provides operational monitoring. Product analytics storage/provider selection remains open.

## Reliability and consistency

The architecture must make the paid occurrence auditable without presenting a balance. Platform purchase verification, idempotency, local scheduling, server state, completion, refunds, and restore/reinstall behavior require explicit reconciliation rules.

## Open Questions

- Is an account required, or is the first version anonymous/per-device?
- Is more than one active purchased commitment permitted?
- Which service is authoritative for each state: app, platform store, or backend?
- How are StoreKit and Google Play purchases verified and reconciled with server state?
- What idempotency keys and retry rules prevent duplicate commitments?
- What is the state transition for cancelled, pending, revoked, or refunded purchases?
- How are local notifications restored after reinstall, device migration, or notification loss?
- What happens when notification scheduling fails after a verified purchase?
- How are paid notifications rescheduled, and which timestamp/timezone representation is authoritative?
- How does a timezone change affect the intended local time versus absolute instant?
- Is EventBridge needed for reconciliation/fallback only, or is local notification delivery sufficient for MVP?
- Which analytics backend is used, and what privacy/consent requirements apply?
- How should web payments map to the same commitment and reconciliation model?

