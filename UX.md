# UX

## Principle

The MVP should work primarily as state transitions within one screen. Simplicity means eliminating meaningless work, not removing the payment or explicit completion actions that create product value.

The interface must not require unnecessary interpretation or choice. It must show the exact date and time that will trigger the notification.

## State 1: Purchase setup

Example:

```text
8/31
07:00

# UX

## 1. UX objective

The UX should make one commitment feel concrete without turning the product into a form, dashboard, or habit-management system.

The target is not “fewest possible taps at all costs.” The target is:

> Remove every interaction that does not strengthen commitment or clarify what will happen.

Payment, explicit completion, and a clear next purchase are meaningful interactions and should remain visible.

## 2. One-screen model

The MVP should be designed as a single primary screen with state transitions rather than a stack of management screens.

Recommended conceptual states:

```text
READY_TO_PURCHASE
→ PURCHASING
→ SCHEDULED
→ DUE
→ COMPLETED_NEXT_READY
→ PURCHASING ...
```

Transient platform states such as payment cancellation, payment pending, or notification-permission errors may overlay this flow, but should not become separate product areas.

## 3. State A: Ready to purchase

Primary content:

```text
8/31
07:00

×1
¥100

購入
```

### Required behavior

- Show the **actual calendar date** and **actual time** that will be scheduled.
- Make date/time editable with minimal friction.
- Show one selected multiplier and its exact price.
- Allow switching between ×1 / ×10 / ×100.
- Keep the purchase action visually clear.
- Do not add habit/task text fields.

### Multiplier selection

The three choices are peers.

Do not add:

- “おすすめ”
- “人気”
- “ベスト”
- savings percentages
- visual pressure toward ×10 or ×100

The amount itself is the commitment mechanism.

## 4. Date and time interaction

### Principle

The user should never have to solve a date puzzle.

Bad interaction:

```text
07:00
※ 過ぎていたら明日
```

Better interaction:

```text
8/31
07:00
```

The system may use sensible defaults internally, but the final interpreted date/time must be displayed before payment.

### Defaulting

When preparing the next commitment after completion:

- derive a sensible next date/time from the previous commitment,
- render the resulting concrete date and time,
- allow the user to change it,
- avoid presenting a large list of date suggestions.

Do not create a choice buffet such as “tomorrow / weekday / weekend / 3 days / next week / custom” unless later evidence shows it is necessary.

## 5. Purchase interaction

Payment is not an implementation nuisance to hide. It is the product moment.

### Before invoking store payment

The user should be able to see, at minimum:

- scheduled date,
- scheduled time,
- multiplier,
- exact localized price.

### After successful payment

Transition immediately into the scheduled state.

```text
8/31 07:00
✓
```

The check indicates that this commitment has been purchased/scheduled, not that the real-world action is complete.

### Payment cancellation/failure

Keep the user on the ready-to-purchase state with their chosen date/time and multiplier preserved where safe.

Use compact error language. Do not turn a cancelled payment into an onboarding or recovery flow.

## 6. State B: Scheduled

Example:

```text
8/31 07:00
✓
```

Primary purpose:

- confirm that a paid commitment exists,
- show exactly when the command will occur.

Avoid filling the waiting state with productivity content, encouragement, stats, or countdown theatrics.

## 7. Notification

### Copy

Japanese:

```text
やれ！
```

English:

```text
DO IT.
```

Other locales should preserve the same minimal function rather than translating into a motivational paragraph.

### Notification semantics

Notification delivery/open is an event, not success.

Do not:

- auto-complete on delivery,
- auto-complete on tap,
- infer completion from app open,
- award anything merely for opening.

## 8. State C: Due

When the user enters from the notification or opens an overdue actionable commitment:

```text
やれ！

[ 完了 ]
```

The screen intentionally contains almost nothing else.

The product is asking the user to leave the screen and act, then come back to deliberately close the loop.

## 9. Completion interaction

The **完了** button is a meaningful declaration.

On press:

1. persist completion,
2. emit `completion_pressed`,
3. transition to acknowledgement,
4. prepare the next purchase defaults.

Do not treat completion as an undoable destructive action unless later product requirements introduce a correction model. If an undo/correction feature is considered, it belongs in Open Questions first.

## 10. State D: Completed / next purchase ready

Example:

```text
よくやった

9/1
07:00

×1
¥100

購入
```

### Principle

The acknowledgement and next commitment should be adjacent in time and space.

Do not send the user to a history screen or dashboard between completion and the next purchase opportunity.

### Next defaults

Use the previous commitment as the starting point:

- prefill the next date/time using the agreed next-date rule,
- preselect the previous multiplier if confirmed by product decision,
- show the exact next date/time and price,
- require a fresh purchase.

## 11. Notification permissions UX

Notification permission is necessary infrastructure, but onboarding must remain minimal.

Preferred approach:

- ask only when the user has enough context to understand why notifications are essential,
- explain in one short sentence if a pre-permission screen is required by platform UX,
- if permission is denied, show the shortest route to recovery necessary to make the paid product function safely.

A paid commitment must not appear successfully scheduled if the app cannot actually schedule/deliver the required notification under the current permission/state model.

The exact product behavior for denied permissions before/after purchase is an Open Question because it affects payment safety.

## 12. Accessibility and clarity

Minimal UI still needs robust usability.

Requirements:

- large touch targets,
- screen-reader labels for date/time, multiplier, price, purchase, and completion,
- dynamic text support where practical,
- high contrast,
- do not encode purchased/completed state by color alone,
- localize date/time formatting while preserving the exact scheduled moment.

Accessibility should not introduce additional product concepts.

## 13. Localization

Localize:

- “やれ！” / equivalent command,
- “完了”,
- “よくやった”,
- “購入”,
- prices/currency,
- date/time presentation,
- payment/error/system copy.

Keep core command/acknowledgement strings extremely short.

## 14. UX analytics mapping

| Transition | Event |
|---|---|
| app foreground/open | `app_opened` |
| tap purchase before store sheet | `purchase_started` |
| verified purchase succeeds | `purchase_completed` |
| local notification successfully scheduled | `notification_scheduled` |
| user opens from notification | `notification_opened` |
| user explicitly presses completion | `completion_pressed` |
| purchase succeeds from completed/next state | `next_purchase_completed` |

The distinction between first/general purchase and next purchase should be deterministic in analytics, not guessed after the fact.

## 15. UX anti-patterns

Do not add UI simply because habit apps usually contain it.

Examples to reject in MVP:

- task-name field above the date,
- motivational quote below “やれ！”,
- weekly calendar strip,
- progress ring,
- streak counter,
- notification text editor,
- “popular” pill on ×10,
- preloaded wallet to reduce store-payment friction,
- multi-step onboarding carousel,
- post-completion share screen.

## 16. Open Questions

1. **One active commitment vs multiple:** This materially changes whether the primary screen needs a list. The one-screen vision strongly favors a single active item, but that is not explicitly specified and should not be assumed.
2. **Editing after purchase:** Can a purchased scheduled date/time be changed? If yes, under what constraints? If no, the UX should communicate that before payment without adding friction.
3. **Cancellation after purchase:** Is there any user-facing cancel action for a scheduled commitment, or does store refund policy handle exceptional cases only?
4. **Notification permission denied:** Should purchase be blocked until notification capability is confirmed? This is likely safest for the product promise, but needs an explicit decision.
5. **Notification scheduling limits:** Mobile OS limits and exact-alarm behavior may constrain how far ahead or how precisely a local notification can be guaranteed. UX copy must match real platform behavior.
6. **Missed notification state:** Define the screen if a command is overdue but incomplete.
7. **Completion before due time:** Define whether the completion button is unavailable until due.
8. **Next-date algorithm:** “Previous schedule based” needs one deterministic rule for irregular schedules, month boundaries, DST/time-zone changes, and long gaps.
9. **Time-zone travel:** Decide whether a purchased commitment is anchored to an absolute instant or local wall-clock time if the user changes time zone after purchase.
10. **Accessibility vs extreme minimalism:** Confirm any brand typography/animation choices only after ensuring they do not obscure the core state.
×1
¥100

購入
```

The user sets the actual future date and time, selects ×1, ×10, or ×100, sees the localized price, and purchases that occurrence.

### Date and time

- Display the concrete notification date and time.
- Keep date suggestions to the minimum necessary.
- Do not make the user infer that a past time means tomorrow.
- Prefill the next occurrence from the previous occurrence when available.
- Require explicit confirmation of the actual future date/time before payment.

### Multiplier

- Present ×1, ×10, and ×100 neutrally.
- Update the displayed price immediately.
- Do not add “recommended,” “popular,” or visual pressure toward a tier.

### Purchase

- Every scheduled occurrence is purchased individually.
- Do not route the user through credits, balances, subscriptions, or bundles.
- The purchase action must clearly apply to the displayed notification date/time and multiplier.

## State 2: Purchased

Example:

```text
8/31 07:00
✓
```

Show the exact scheduled date/time and a clear purchased/scheduled state. Do not treat this checkmark as completion of the real-world action.

## State 3: Notification due

Example:

```text
やれ！

[ 完了 ]
```

The notification and destination state use the minimal localized command. Opening the notification does not complete the occurrence.

## State 4: Completion

The user presses “完了” only after performing the action. This explicit action is required and produces the completion event.

## State 5: Acknowledgement and next purchase

Example:

```text
よくやった

9/1
07:00

×1
¥100

購入
```

Immediately after acknowledgement, show a ready-to-buy next occurrence. Prefill the date, time, and multiplier from the prior occurrence, while displaying the actual next notification date/time clearly.

## State transitions

`purchase setup → purchased → notification due → completion → acknowledgement/next purchase`

The acknowledgement/next-purchase state returns to the same purchase loop; it is not a dashboard.

## Error and edge-state requirements

Before implementation, define visible states for:

- notification permission denied or unavailable;
- payment pending, failed, cancelled, or refunded;
- notification scheduling failure;
- app opened after the scheduled time;
- device time or timezone changes;
- purchase restored or server/device state mismatch.

These states must preserve clarity about whether money was charged and whether a notification is scheduled.

## Open Questions

- Can the purchased state contain one future occurrence or a list of multiple occurrences?
- Must notification permission be granted before the purchase button is enabled?
- What exact next-date rule is used after completion: fixed interval, prior local clock time, or another rule?
- Can the user edit or reschedule a paid notification? If so, under what constraints?
- What happens when the chosen time passes during payment?
- How should daylight-saving and timezone changes be explained without requiring user interpretation?
- What happens if a notification is opened on one device and completion occurs on another?
- What recovery path is shown when payment succeeds but local notification scheduling fails?

