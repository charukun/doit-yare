# doit-yare

doit-yare is a habit-forming and action-prompting app that lets users buy a single future command to themselves. For every scheduled notification, the user chooses a date, time, and commitment multiplier, pays real money for that one occurrence, receives a minimal “やれ！” / “DO IT.” prompt, performs the action, and explicitly marks it complete.

The product is currently in planning. **Implementation has not started.** These documents define the MVP baseline before application code is written.

## Project plan

- [Product](PRODUCT.md) — product principles, loop, pricing, KPIs, exclusions, and open questions
- [UX](UX.md) — one-screen state model and interaction rules
- [Architecture](ARCHITECTURE.md) — proposed monorepo, clients, backend, payments, notifications, and data model
- [Launch](LAUNCH.md) — five-day development target, QA, store submission, promotion, and launch KPIs

# doit-yare

A commitment-device app where each future **「やれ！」** notification is purchased individually with real money.

The product is intentionally narrow: the value comes from paying for **this one future command**, acting when it arrives, explicitly marking the action complete, then choosing whether to buy the next one.

## Project plan

- [PRODUCT.md](./PRODUCT.md) — product principles, scope, pricing, analytics, and explicit non-goals
- [UX.md](./UX.md) — one-screen state model, interaction rules, copy, and edge-case UX
- [ARCHITECTURE.md](./ARCHITECTURE.md) — proposed monorepo and MVP technical architecture
- [LAUNCH.md](./LAUNCH.md) — five-day build target, store submission, testing, and launch/marketing plan

## Core loop

1. Choose the actual notification date and time.
2. Choose commitment strength: ×1 / ×10 / ×100.
3. Pay for that single notification.
4. At the specified time, receive **「やれ！」**.
5. Do the action in the real world.
6. Explicitly press **「完了」**.
7. See **「よくやった」**.
8. The next purchase is prefilled from the previous one.
9. Buy the next notification only if the user chooses to continue.

## MVP priority

1. iOS
2. Android
3. Web / PWA after mobile is complete

Apple Watch and Wear OS use phone-notification mirroring in the first release. Dedicated watch apps are post-MVP.

## Current phase

Planning only. Application implementation should not begin until the documents above are reviewed and the open questions are resolved where they materially affect implementation.
