# PRODUCT

## 1. Product definition

`doit-yare` is a habit/action commitment app built around a deliberately unusual transaction:

> The user pays real money every time they schedule one future command to themselves.

It is closer to a **commitment device** than a conventional habit tracker.

The product is not trying to minimize every tap. It removes only interactions that do not improve the psychological effect or clarity of the commitment.

## 2. Product promise

The user should feel:

- “I paid for this specific future moment.”
- “The notification is not generic. I deliberately bought it.”
- “Opening the notification is not success. I still have to do the thing.”
- “Pressing completion is a conscious declaration after the action.”
- “I decide whether the next commitment is worth buying.”

The central product value is therefore **per-event real-money commitment**, not tracking, optimization, rewards, or content.

## 3. Non-negotiable principles

### 3.1 Every commitment is purchased separately

Each scheduled notification requires a fresh payment at the time it is created.

Do not replace this with:

- stored credits,
- prepaid balance,
- bundles,
- subscriptions,
- automatic renewal,
- “free uses” funded from previously purchased balance.

The psychological contract is: **“I am paying for this one command.”**

### 3.2 The command is intentionally minimal

The default notification copy is:

- Japanese: **やれ！**
- English: **DO IT.**

Other locales should use similarly short, forceful language.

The user does not need to write a habit name, task title, or custom command.

### 3.3 Notification open is not completion

Opening or tapping the notification must never mark the commitment complete.

The intended sequence is:

1. notification arrives,
2. user sees “やれ！”,
3. user acts in the real world,
4. user returns and explicitly presses “完了”.

That explicit action is part of the product effect.

### 3.4 Completion receives a minimal acknowledgement

After completion, show:

**よくやった**

No points, score, streak, badge, confetti economy, or gamified reward is required for MVP.

### 3.5 The next commitment is easy, but not automatic

Immediately after completion, offer the next purchase.

The next date/time and multiplier should be prefilled from the previous commitment where this can be done without ambiguity.

The user must still make a new purchase. Nothing renews automatically.

### 3.6 Show the actual date and time

Do not make the user mentally translate rules such as:

- “if the selected time is earlier than now, it means tomorrow”,
- “same time next day unless…”,
- ambiguous relative-date shortcuts.

The UI should display the concrete notification date and time that will actually be scheduled.

## 4. Core loop

```text
Choose date/time
→ choose multiplier
→ pay for this one commitment
→ notification at scheduled time: “やれ！”
→ user acts
→ user presses “完了”
→ “よくやった”
→ next date/time + multiplier are prefilled
→ user optionally buys the next commitment
```

## 5. Pricing

### Japan baseline

| Product | Multiplier | Price |
|---|---:|---:|
| `commit_x1` | ×1 | ¥100 |
| `commit_x10` | ×10 | ¥1,000 |
| `commit_x100` | ×100 | ¥10,000 |

The multiplier represents commitment intensity through the amount paid.

Do not label any multiplier as:

- recommended,
- most popular,
- best value,
- smart choice,
- default because of conversion optimization.

The user chooses how strongly to bind themselves.

### International pricing

Prices should be localized to appropriate regional price points while preserving the three-level commitment concept.

Exact App Store / Google Play price points should be finalized against the current store configuration available at implementation time.

## 6. MVP platforms

### First priority

- iOS
- Android

### Wearables in first release

- Apple Watch: iPhone notification mirroring
- Wear OS: Android notification mirroring

Dedicated watch applications are post-MVP.

### Later

- Web / PWA after mobile is complete

The web product must preserve per-event payment. It must not introduce a credit wallet as a workaround for payment friction.

## 7. MVP product states

The product should work primarily as one screen moving through a small state machine.

### A. Ready to purchase

```text
8/31
07:00

×1
¥100

購入
```

### B. Purchased / scheduled

```text
8/31 07:00
✓
```

### C. Due / notification opened

```text
やれ！

[ 完了 ]
```

### D. Completed / next purchase ready

```text
よくやった

9/1
07:00

×1
¥100

購入
```

## 8. Analytics

### Minimum events

- `app_opened`
- `purchase_started`
- `purchase_completed`
- `notification_scheduled`
- `notification_opened`
- `completion_pressed`
- `next_purchase_completed`

Events should include only the properties required to operate and measure the MVP. Avoid analytics-driven product expansion before the core loop is validated.

### Primary KPIs

- first-purchase conversion rate
- notification → completion rate
- completion → next-purchase rate
- 7-day repeat-purchase rate
- ×1 / ×10 / ×100 purchase mix

### Most important transition

`completion_pressed → next_purchase_completed`

This measures whether the completed commitment naturally creates willingness to buy the next commitment.

## 9. DO NOT ADD

This section is intentionally explicit so human or AI implementation does not “improve” the MVP into a different product.

**DO NOT ADD to the MVP unless the product specification is deliberately revised:**

- habit name
- custom command text
- category
- streak
- graph
- calendar-style management screen
- ranking
- social network features
- community
- AI coach
- points
- badges
- credits
- prepaid balance
- subscription
- game mechanics
- complex onboarding

Also do not silently introduce functional equivalents under different names.

Examples:

- “tokens” are credits and are not allowed.
- “monthly commitment plan” is a subscription and is not allowed.
- “daily win counter” is a streak mechanic and is not allowed.
- “goal title” is a habit/task name and is not allowed.

## 10. Product acceptance test

Before adding any interaction, ask:

1. Does this interaction materially strengthen commitment, payment clarity, scheduling clarity, completion intent, or the next-purchase loop?
2. If removed, does the psychological effect become weaker or the transaction become confusing/unsafe?
3. Is it merely conventional habit-app furniture?

If the answer to #3 is yes and #1/#2 are no, it should not be added.

## 11. Open Questions

These are unresolved implementation/product questions. They must not be answered by silently changing the product philosophy.

1. **Concurrent commitments:** Can a user have more than one purchased future commitment at the same time, or is MVP intentionally one active commitment at a time?
2. **Completion timing:** May the user press completion before the scheduled time, or only after the notification becomes due?
3. **Missed commitments:** If the scheduled time passes and the user never completes it, does it remain actionable indefinitely, become missed, or require another explicit state?
4. **Next-date rule:** The example implies “same time next day”, but the exact prefill rule has not been defined for all prior schedules. The UI must show the concrete resulting date/time rather than make the user infer it.
5. **Multiplier persistence:** The brief says next date/time and multiplier are based on the previous purchase. Confirm whether the previous multiplier is always the default selection.
6. **Account model:** Is MVP anonymous/device-based, Sign in with Apple/Google, email-based, or another model? This affects cross-device server state and purchase reconciliation.
7. **Refund / revoked purchase behavior:** What happens to an already scheduled commitment if the store later reports a refund, revocation, chargeback, or failed settlement?
8. **Regional pricing:** Final localized price points for `commit_x1`, `commit_x10`, and `commit_x100` need to be selected in App Store Connect / Play Console rather than inferred in code.
9. **Web payment provider:** The web version requires a provider and settlement/refund model, but no provider has been selected yet.
10. **Product name / store-facing brand:** `doit-yare` is the repository name. Final app/store name and localized naming remain open unless already decided elsewhere.
