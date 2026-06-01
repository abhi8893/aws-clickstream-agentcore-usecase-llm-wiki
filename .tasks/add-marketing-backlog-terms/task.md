I was reading the archiecture guidance and found this usecase.

Add marketing terms like identity resolution, cross-channel attribution, and unified funnel analysis to the backlog

```

Real-World Architectural Scenarios
Scenario A: Unified Cross-Platform Ecosystem (Multiple Apps, Single Project)
Imagine a ride-hailing and food delivery platform.

Project: SuperApp-Production (Deployed in a single region to consolidate all regional consumer data).

App 1 (Web Portal): Captures desktop browsing behavior when users log into their accounts via Chrome or Safari.

App 2 (iOS Consumer App): Captures native mobile interactions, device telemetry, and Apple Pay checkouts.

App 3 (Android Driver App): Captures distinct workflows unique to drivers, background location updates, and operational state changes.

Why this hierarchy works: The data science and marketing teams need a holistic view of the user journey. If a user browses a promotion on the web portal (App 1) and subsequently opens the iOS app (App 2) to complete a transaction, the events must land in the same analytical database. This enables identity resolution, cross-channel attribution, and unified funnel analysis without needing to join completely separate data warehouses.
```

