# Research Backlog

A running list of topics encountered during research that are worth tracking but **not yet ready for deep investigation**.

**Hard rule:** Never create wiki pages, run `/wiki-process`, or conduct any research on items in this list unless the user explicitly promotes them via `/wiki-backlog promote <slug>` or says "research backlog: `<slug>`".

Use `/wiki-backlog add "<topic>"` to append entries, or edit this file directly.

## Priority levels

| Value | Meaning |
|---|---|
| `near` | Parked temporarily — expected to be needed soon, just not right now |
| `distant` | Tangential encounter — no clear timeline, promote only when relevant work begins |
| `if-needed` | Very tangential — only promote with a concrete forcing reason |

---

## Topics

| Slug | One-liner | Priority | Domain | Tags | Concepts | Components | Context | Date |
|---|---|---|---|---|---|---|---|---|
| `identity-resolution` | Linking user identities across devices, platforms, and anonymous/authenticated sessions | near | Marketing Analytics / Identity | identity, cross-device, user-stitching | device graph, identity stitching, probabilistic/deterministic matching | — | From SuperApp architecture scenario — needed when users browse on web (App 1) then transact on iOS (App 2); events must be tied to the same user without joining separate data warehouses | 2026-06-01 |
| `cross-channel-attribution` | Attributing conversions to touchpoints across web, iOS, Android, and other channels | near | Marketing Analytics | attribution, multi-touch, marketing | last-touch, first-touch, multi-touch attribution models | — | From SuperApp architecture scenario — needed to credit which channel drove a conversion when a user browses on web then completes a purchase on mobile | 2026-06-01 |
| `unified-funnel-analysis` | Analyzing user conversion funnels stitched across multiple apps and platforms | near | Product Analytics / Marketing | funnel, conversion, cross-platform | funnel steps, drop-off analysis, cross-platform sessions | — | From SuperApp architecture scenario — enables funnel analysis without joining separate data warehouses; requires a unified event stream from all apps in a single project | 2026-06-01 |
