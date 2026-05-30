# thumb-first

A **mobile review suite** — three skills that answer the two questions every mobile screen has to pass: *is this the right design?* and *is it built correctly for the device?*

## The Problem

Most "mobile" tooling collapses two very different questions into one. *Is this the right design?* is a judgment question — grip ergonomics, thumb zones, which pattern fits. *Is it built correctly?* is a technical one — safe areas, viewport bugs, service workers, performance. Mixed together, opinions start reading as defects and must-fixes read as preferences.

They also have different shelf-lives. Design judgment is durable; platform specifics rot with every OS and browser release. So **thumb-first** keeps them as separate, independently-evolving skills, with an umbrella that runs both and merges their output — opinions and objective defects kept visually distinct.

## What It Does

| Skill | Layer | Answers | Output |
|-------|-------|---------|--------|
| **`thumb-first`** | Umbrella | — | Routes to a layer, or runs both and merges into one report |
| **`thumb-first-design`** | Design judgment (platform-agnostic) | *Is this the right mobile design?* | Decision forks + opinions, ranked by user impact |
| **`thumb-first-platform`** | Technical verification (web/PWA-deep, native handoff) | *Is it built correctly for the device?* | Objective defects, P0–P3, with `file:line` |

### Three Modes

The umbrella detects what you want and routes — or you call a layer directly.

- **Full review** (default) — design pass + platform pass, merged into one prioritized report.
- **Design only** — `thumb-first-design`: judgment, lenses, forks.
- **Platform only** — `thumb-first-platform`: objective defects and fixes.

### Eight Designer Lenses *(design layer)*

The design layer thinks in named, attributable voices — not a flattened "best practices" checklist — with **context-aware weighting** (the lenses that lead depend on what you're designing):

| Lens | Voice(s) |
|------|----------|
| Ergonomic Realist | Steven Hoober |
| Thumb-Zone Mapper | Scott Hurff |
| Focus Forcer | Luke Wroblewski |
| Direct Manipulation | Josh Clark |
| Navigation Taxonomist | Frank Rausch |
| Empirical Skeptic | Raluca Budiu / NN/g |
| Modern Craft | Andy Allen / Sebastiaan de With / Gavin Nelson |
| Platform Canon | Apple HIG / Material 3 |

Where the lenses *disagree* (Hoober vs. Hurff on placement, Allen vs. de With on personality), the skill surfaces the disagreement as a decision rather than papering over it.

### Decision Forks *(design layer)*

The core discipline. Nine recurring mobile decisions with no universal right answer — primary navigation, sheet vs. modal, gesture vs. visible control, action placement, list vs. grid, long-form structure, information density, where destructive actions hide, how to onboard. Each comes with options, honest tradeoffs, and the signals that point one way or the other. Audit mode emits a self-contained HTML report with a collision-proof thumb-zone diagram and severity-tiered findings.

### Platform Verification *(platform layer)*

Finds and fixes *objective* defects in mobile web and PWAs — the things that are right or wrong, not matters of taste: viewport and safe-area handling, iOS Safari quirks, Android/Chrome behavior, touch interactions, responsive patterns, PWA manifest and service workers, push notifications, image and runtime performance. Web/PWA-deep, with an honest handoff (never a faked audit) when the target is native. Ships fix workflows, not just findings.

## Core Principles

1. **Design for the most constrained grip** *(Hoober)* — grips switch constantly; "49% use one hand" is not license to bury controls.
2. **Thumb zones are a cost map, not a layout grid** *(Hurff)* — high-cost zones are the right home for rare and destructive actions.
3. **Mobile forces focus** *(Wroblewski)* — prioritization is the design work; layout is downstream of it.
4. **The content is the control** *(Clark)* — prefer direct manipulation; every undiscoverable gesture is an experts-only feature.
5. **Motion is communication, not decoration** *(Modern Craft)* — transitions carry state, hierarchy, and causality.
6. **Navigation chrome is currently unsettled** — iOS 26 and Material 3 are mid-transition; flag it, don't canonize it.
7. **Opinions and defects stay distinct** — a preference must never read as a must-fix, or a P0 as a matter of taste.

## Usage

```
/thumb-first                 # full review: design pass + platform pass, merged
/thumb-first design          # design judgment only
/thumb-first platform        # platform / technical only

# Also auto-detected from natural language:
"review my mobile app"
"is this good mobile UX"
"audit this screen"
"check my PWA"
```

Give it a screenshot, a live URL, code, or a description — any one is enough.

## What's Included

```
skills/
├── thumb-first/                        # umbrella — routing + merge only (no domain knowledge)
│   ├── SKILL.md                        # mode selection + two-pass orchestration
│   ├── workflows/
│   │   └── full-review.md              # run both passes → merge
│   └── references/
│       └── unified-report.md           # merged report: forks vs defects, combined fix-order
├── thumb-first-design/                 # design judgment — platform-agnostic
│   ├── SKILL.md                        # principles, lens roster, routing
│   ├── workflows/
│   │   ├── plan.md                     # discovery → prioritize → choose patterns → brief
│   │   └── audit.md                    # assess through lenses → surface forks → findings
│   └── references/
│       ├── designer-lenses.md          # the 8 lenses, disagreements, weighting table
│       ├── reach-and-ergonomics.md     # grip data, thumb zones, action placement
│       ├── navigation.md               # nav taxonomy, tab/drawer/sheet/modal, iOS 26 + M3
│       ├── forms-and-input.md          # single-column forms, input types, field reduction
│       ├── touch-gesture-motion.md     # touch targets, gesture discoverability, motion, haptics
│       ├── content-and-attention.md    # prioritization, scanning, glanceability, overlays
│       ├── design-forks.md             # 9 ambiguous decisions with options and tradeoffs
│       ├── output-format.md            # report governance (HTML default + terminal)
│       └── report-template.html        # canonical self-contained HTML report
└── thumb-first-platform/               # technical verification — web/PWA deep, native handoff
    ├── SKILL.md                        # defect taxonomy, P0–P3, target detection
    ├── workflows/
    │   ├── audit-mobile-pwa.md         # find objective defects with file:line
    │   ├── fix-ios-issues.md           # Safari/viewport/safe-area fixes
    │   ├── optimize-performance.md     # runtime + image performance
    │   ├── setup-pwa.md                # manifest + service worker setup
    │   └── test-mobile.md              # testing & debugging on device
    └── references/                     # ios-safari-quirks, android-pwa, viewport-layout,
                                        #   service-workers, push-notifications, performance, …
```

## Where It Fits

thumb-first decides and verifies; it does **not** write the implementation — it hands off:

- **responsive-craft** — responsive web layout
- **React Native / Expo skills** — native mobile implementation

Decide the design and catch the defects here; hand the brief and fix-list to the right implementation skill.

## Install

```bash
npx skills add kylezantos/thumb-first
```

Installs the suite (all three skills) and auto-detects your agents (Claude Code, Codex, Cursor, OpenCode, Gemini CLI, Windsurf, and more). Manual install — copy each directory under `skills/` into your agent's skills path (e.g. `~/.claude/skills/`); keep the three as siblings so the umbrella's relative references resolve.

## Cross-Agent Compatibility

Degrades gracefully — core functionality works on any AI coding agent. Claude Code users get tappable mode and fork selection via `AskUserQuestion`; other agents get plain-text equivalents.

## License

MIT
