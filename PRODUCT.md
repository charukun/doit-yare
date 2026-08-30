# Product

## Product concept

doit-yare is a habit-forming and action-prompting app. A user pays real money each time they schedule a future notification that tells them to act.

The goal is not to minimize every interaction. It is to remove only interactions that add no value. The payment, the explicit completion action, and the next purchase are meaningful parts of the product.

The product is positioned less as a habit tracker and more as:

- a commitment device that uses real money;
- a way to buy one command to your future self at a time.

## Core product principle

**Per-occurrence payment is the center of the product value.**

The user must retain the psychological commitment: “I paid real money for this specific notification.” Therefore, the product must not convert payments into credits, prepaid balances, bundles, or subscriptions.

## Basic loop

1. Specify a date and time.
2. Select a commitment multiplier.
3. Pay for that occurrence immediately.
4. At the scheduled time, receive “やれ！” / “DO IT.”
5. Perform the intended action.
6. Explicitly press “完了” / “Complete.”
7. See “よくやった” / an equivalent localized acknowledgement.
8. Receive a default next date, time, and multiplier based on the previous purchase.
9. Buy the next occurrence.

## Notification content

Users do not enter a habit name or custom command.

The notification is intentionally minimal:

- Japanese: `やれ！`
- English: `DO IT.`
- Other locales: an equivalently minimal localized command

## Completion

Opening or tapping the notification is not completion. The user presses “完了” only after acting. That explicit self-declaration is a meaningful part of the commitment loop.

After completion, show:

`よくやった`

Then allow the next occurrence to be purchased immediately.

## Next purchase

The next date, time, and multiplier are prefilled from the previous occurrence. Do not present numerous date suggestions or make the user mentally interpret rules such as “a time earlier than now means tomorrow.” Always show the actual notification date and time explicitly.

## Pricing

### Japan

| Multiplier | Price |
| --- | ---: |
| ×1 | ¥100 |
| ×10 | ¥1,000 |
| ×100 | ¥10,000 |

Other regions use suitable localized price points.

Do not label any multiplier “recommended,” “popular,” or similar. The user chooses their own commitment intensity.

## Device strategy

Priority clients:

- iOS
- Android

Initial wearable support:

- Apple Watch through mirrored iPhone notifications
- Wear OS through mirrored Android notifications

Dedicated watch apps are post-MVP.

Web/PWA is planned after the mobile experience is complete.

## Analytics and KPIs

Minimum events:

- `app_opened`
- `purchase_started`
- `purchase_completed`
- `notification_scheduled`
- `notification_opened`
- `completion_pressed`
- `next_purchase_completed`

Primary KPIs:

- first-purchase conversion rate
- notification-to-completion rate
- completion-to-next-purchase rate
- seven-day repeat-purchase rate
- ×1 / ×10 / ×100 mix

The most important KPI is:

`completion_pressed → next_purchase_completed`

## DO NOT ADD

The following must not be added to the MVP without an explicit product decision:

- habit names
- custom command input
- categories
- streaks
- graphs
- calendar-style management screens
- rankings
- social networking
- community features
- AI coaching
- points
- badges
- credits
- prepaid balances
- subscriptions
- game mechanics
- complex onboarding

## Open Questions

- Can a user own only one paid future notification at a time, or multiple notifications?
- Is purchase allowed before notification permission is granted?
- What exact rule determines the prefilled next date and time?
- How are regional price points selected and maintained?
- What is the localized acknowledgement equivalent to “よくやった” in each language?
- Is an account required, or does MVP begin anonymously/on a per-device basis?
- What user-facing state applies after a purchase cancellation or refund?

