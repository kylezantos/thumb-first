# thumb-first

A skill for **mobile design judgment** — deciding *what* the right mobile pattern is and *why*, not how to code it. Platform-agnostic: the decisions hold whether the implementation ends up in CSS, SwiftUI, or React Native.

## The Problem

Most skills and tools for "mobile" are about *implementation* — viewport units, safe-area CSS, service workers, native components. The layer above that — the actual design decisions — usually gets made by default: a hamburger menu because it looks tidy, a primary button at the top because that's the desktop habit, a bottom sheet used as a page because sheets are fashionable.

Those are *judgment* failures, not code failures. And the worst ones aren't wrong answers — they're questions that were never asked.

**thumb-first** is the decision layer. It reasons through named mobile designers, surfaces the choices that have no single right answer, and makes you decide them deliberately.

## What It Does

### Two Modes

- **Plan** (`/thumb-first plan`) — Design a feature's mobile UX before any code: discovery, content prioritization, pattern decisions, then a mobile design brief an implementer can build from.
- **Audit** (`/thumb-first audit`) — Review an existing mobile design through eight lenses, surface the forks that were decided silently, return prioritized findings.

### Eight Designer Lenses

The skill thinks in named, attributable voices — not a flattened "best practices" checklist — with **context-aware weighting** (the lenses that lead depend on what you're designing):

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

The lenses carry real positions — and where they *disagree* (Hoober vs. Hurff on placement, Allen vs. de With on how much personality an interface should carry), the skill surfaces the disagreement as a decision rather than papering over it.

### Decision Forks

The skill's core discipline. Nine recurring mobile decisions with no universal right answer — primary navigation, sheet vs. modal, gesture vs. visible control, action placement, list vs. grid, long-form structure, information density, where destructive actions hide, how to onboard the interface. Each comes with options, honest tradeoffs, and the signals that point one way or the other.

## Core Principles

1. **Design for the most constrained grip** *(Hoober)* — grips switch constantly; "49% use one hand" is not license to bury controls.
2. **Thumb zones are a cost map, not a layout grid** *(Hurff)* — high-cost zones are the right home for rare and destructive actions.
3. **Mobile forces focus** *(Wroblewski)* — prioritization is the design work; layout is downstream of it.
4. **The content is the control** *(Clark)* — prefer direct manipulation; every undiscoverable gesture is an experts-only feature.
5. **Motion is communication, not decoration** *(Modern Craft)* — transitions carry state, hierarchy, and causality.
6. **Navigation chrome is currently unsettled** — iOS 26 and Material 3 are mid-transition; flag it, don't canonize it.
7. **Surface decision forks** — present options with tradeoffs; don't default silently.

## What's Inside

```
thumb-first/
├── SKILL.md                          # principles, lens roster, routing, gotchas
├── workflows/
│   ├── plan.md                       # discovery → prioritize → choose patterns → brief
│   └── audit.md                      # assess through lenses → surface forks → findings
├── references/
│   ├── designer-lenses.md            # the 8 lenses, their disagreements, weighting table
│   ├── reach-and-ergonomics.md       # grip data, thumb zones, action placement
│   ├── navigation.md                 # nav taxonomy, tab/drawer/sheet/modal, iOS 26 + M3
│   ├── forms-and-input.md            # single-column forms, input types, field reduction
│   ├── touch-gesture-motion.md       # touch targets, gesture discoverability, motion, haptics
│   ├── content-and-attention.md      # prioritization, scanning, glanceability, overlays
│   └── design-forks.md               # 9 ambiguous decisions with options and tradeoffs
└── README.md
```

## Where It Fits

thumb-first decides the design. It does **not** write the implementation — it hands off:

- **responsive-craft** — responsive web layout
- **mobile-design-audit** — mobile-web / PWA rendering, safe areas, service workers
- **React Native / Expo skills** — native mobile implementation

If you want the mobile *design* decided well, start here; then hand the brief to the right implementation skill.

## Install

```bash
npx skills add kylezantos/thumb-first
```

Auto-detects installed agents and installs to each (Claude Code, Codex, Cursor, OpenCode, Gemini CLI, Windsurf, and more).

Manual install — copy the `thumb-first/` directory into your agent's skills path (e.g. `~/.claude/skills/thumb-first/`).

## Usage

```
/thumb-first plan          # design a feature's mobile UX
/thumb-first audit         # review an existing mobile design

# Also auto-detected from natural language:
"design the mobile UX for this"
"how should this work on mobile"
"review this mobile screen"
"is this good mobile design"
```

## Cross-Agent Compatibility

Degrades gracefully — core functionality works on any AI coding agent. Claude Code users get tappable mode and fork selection via AskUserQuestion; other agents get plain-text equivalents.

## License

MIT
