# Changelog

All notable changes to this skill will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.1.0] — 2026-05-30

Restructured from a single mobile-design skill into a **three-skill mobile review suite**. The design-judgment behavior is unchanged — it's now one layer of three — and a new platform-verification layer plus an umbrella that merges both are added alongside it.

### Added
- **`thumb-first-platform`** — a new technical-verification layer for mobile web and PWAs. Finds and fixes *objective* platform defects (P0–P3, with `file:line`): viewport and safe-area handling, iOS Safari quirks, Android/Chrome behavior, touch interactions, responsive patterns, PWA manifest and service workers, push notifications, and image/runtime performance. Web/PWA-deep, with an honest handoff (never a faked audit) when the target is native. Ships fix workflows, not just findings.
- **`thumb-first` umbrella** — a one-stop entry point that routes to a single layer (design-only / platform-only) or runs both and merges them into one prioritized report, keeping design forks (opinions) and platform defects (objective) visually distinct. Routing + merge only; it holds no domain knowledge of its own.

### Changed
- **Repo restructured into a `skills/` monorepo** (`skills/thumb-first/`, `skills/thumb-first-design/`, `skills/thumb-first-platform/`). The original root-level monolith became `thumb-first-design`, moved with its git history preserved. The three skills are siblings so the umbrella's relative references resolve in-repo and when installed.
- README rewritten for the suite.

> Packaging note: the install path changed (root skill → `skills/<name>/`). Re-install via `npx skills add kylezantos/thumb-first`, which now installs all three.

## [1.0.0] — 2026-05-16

Initial release — a single **mobile design-judgment** skill.

### Added
- Plan and Audit modes for mobile UX, reasoning through eight named designer lenses (Hoober, Hurff, Wroblewski, Clark, Rausch, Budiu/NN/g, Modern Craft, Platform Canon) with context-aware weighting.
- Nine decision forks — recurring mobile choices with no single right answer, surfaced with options and tradeoffs rather than defaulted silently.
- Governed self-contained HTML audit report with a collision-proof thumb-zone diagram and severity-tiered findings.
