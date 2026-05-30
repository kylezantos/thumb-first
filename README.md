# thumb-first

A **mobile review suite** — three skills that, together, answer the two questions every mobile screen has to pass: *is this the right design?* and *is it built correctly for the device?*

The two questions have different shelf-lives. Design judgment is durable — grip ergonomics and thumb zones don't change when Safari ships a point release. Platform specifics rot with every OS and browser update. So they live as **separate, independently-evolving skills**, with an umbrella that runs both and merges their output — keeping opinions (design forks) and objective defects (P0–P3) visually distinct so a preference never reads as a must-fix.

## The suite

| Skill | Layer | Answers | Output |
|-------|-------|---------|--------|
| **`thumb-first`** | Umbrella | — | Routes to a layer, or runs both and merges into one report |
| **`thumb-first-design`** | Design judgment (platform-agnostic) | *Is this the right mobile design?* | Decision forks + opinions, ranked by user impact |
| **`thumb-first-platform`** | Technical verification (web/PWA-deep, native handoff) | *Is it built correctly for the device?* | Objective defects, P0–P3, with `file:line` |

```
skills/
├── thumb-first/            # umbrella — routing + merge only (no domain knowledge)
├── thumb-first-design/     # 8 designer lenses, 9 decision forks, the report template
└── thumb-first-platform/   # iOS Safari / Android / PWA / performance verification
```

## Usage

The umbrella detects what you want and routes accordingly — or you can call a layer directly.

```
/thumb-first                 # full review: design pass + platform pass, merged
/thumb-first design          # design judgment only
/thumb-first platform        # platform/technical only

# Also auto-detected from natural language:
"review my mobile app"
"is this good mobile UX"
"audit this screen"
"check my PWA"
```

Give it a screenshot, a live URL, code, or a description — any one is enough.

## The design layer — `thumb-first-design`

Decides *what* the right mobile pattern is and *why*, not how to code it. Platform-agnostic: the decisions hold whether the implementation ends up in CSS, SwiftUI, or React Native.

**Eight designer lenses**, with context-aware weighting (the lenses that lead depend on what you're designing) — Steven Hoober (ergonomic realist), Scott Hurff (thumb-zone mapper), Luke Wroblewski (focus forcer), Josh Clark (direct manipulation), Frank Rausch (navigation taxonomist), Raluca Budiu / NN/g (empirical skeptic), Andy Allen / Sebastiaan de With / Gavin Nelson (modern craft), and Apple HIG / Material 3 (platform canon). Where the lenses *disagree*, the skill surfaces the disagreement as a decision rather than papering over it.

**Decision forks** are the core discipline: nine recurring mobile choices with no universal right answer — primary navigation, sheet vs. modal, gesture vs. visible control, action placement, list vs. grid, long-form structure, information density, where destructive actions hide, how to onboard. Each comes with options, honest tradeoffs, and the signals that point one way or the other.

Audit mode emits a **self-contained HTML report** (`references/report-template.html`) with a collision-proof thumb-zone diagram and severity-tiered findings.

## The platform layer — `thumb-first-platform`

Finds and fixes *objective* platform defects in mobile web and PWAs — the things that are right or wrong, not matters of taste: viewport and safe-area handling, iOS Safari quirks, Android/Chrome behavior, touch interactions, responsive patterns, PWA manifest and service workers, push notifications, image and runtime performance. Web/PWA-deep, with an honest handoff (never a faked audit) when the target is native. Ships fix workflows, not just findings.

## How it fits

thumb-first decides and verifies; it does **not** write the implementation — it hands off:

- **responsive-craft** — responsive web layout
- **React Native / Expo skills** — native mobile implementation

## Install

```bash
npx skills add kylezantos/thumb-first
```

Installs the suite (all three skills) and auto-detects your agents (Claude Code, Codex, Cursor, OpenCode, Gemini CLI, Windsurf, and more). Manual install — copy each directory under `skills/` into your agent's skills path (e.g. `~/.claude/skills/`); keep the three as siblings so the umbrella's relative references resolve.

## Cross-agent compatibility

Degrades gracefully — core functionality works on any AI coding agent. Claude Code users get tappable mode and fork selection via `AskUserQuestion`; other agents get plain-text equivalents.

## License

MIT
