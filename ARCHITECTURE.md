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

