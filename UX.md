# UX

## Principle

The MVP should work primarily as state transitions within one screen. Simplicity means eliminating meaningless work, not removing the payment or explicit completion actions that create product value.

The interface must not require unnecessary interpretation or choice. It must show the exact date and time that will trigger the notification.

## State 1: Purchase setup

Example:

```text
8/31
07:00

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

