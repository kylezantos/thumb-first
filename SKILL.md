---
name: thumb-first
description: "Mobile design judgment — the decision layer for designing mobile UX, platform-agnostic across web and native. Two modes: plan a feature's mobile design before code, or audit an existing mobile design. Thinks in named designer lenses (Hoober, Hurff, Wroblewski, Clark, Rausch, Budiu, Allen, de With, Nelson) with context-aware weighting, and surfaces decision forks where there's no single right answer. Use when designing or reviewing mobile interfaces, choosing navigation patterns (tab bar, drawer, bottom sheet), placing actions for thumb reach, designing mobile forms, deciding gestures vs visible controls, or when the user mentions mobile design, mobile UX, thumb zones, one-handed use, bottom sheets, mobile navigation, or touch targets."
argument-hint: "[plan|audit]"
---

# Thumb-First

Mobile design judgment — *what* the right mobile pattern is and *why*, not how to code it. The decisions hold whether the implementation is CSS, SwiftUI, or React Native.

## Quick Start

**Plan a feature's mobile design:** `/thumb-first plan` or "design the mobile UX for this" or "how should this work on mobile"
**Audit an existing mobile design:** `/thumb-first audit` or "review this mobile screen" or "is this good mobile design"

This skill decides patterns and ergonomics. It does not write the implementation — for responsive CSS use `responsive-craft`; for mobile-web/PWA rendering use `mobile-design-audit`; for React Native/Expo use the native-mobile skills.

---

## Core Principles

These are the durable principles — synthesized from the people who defined the field. They survive device generations and platform churn.

1. **Design for the most constrained grip** *(Hoober)* — Grips switch every few seconds with the task. The often-quoted "49% use one hand" is **not** license to bury controls in easy-reach zones. Accommodate the hardest grip the user might be in, so the interface works no matter how they hold the device. *In practice:* keep the interaction surface — controls, nav, primary actions — in the lower band; let content fill the top, but don't put interaction there.

2. **Thumb zones are a cost map, not a layout grid** *(Hurff)* — Natural / Stretch / "Ow" tells you the *cost* of a location, not where things must go. High-cost zones are the right home for rare or destructive actions. Reach cost rises non-linearly with screen size — re-map it per device class.

3. **Mobile forces focus** *(Wroblewski)* — The small screen is a feature: it forces the question "what actually earns this screen?" Decide what to cut and what is primary *before* deciding layout. Prioritization is the design work.

4. **The content is the control** *(Clark)* — Prefer direct manipulation of content over chrome wrapped around it. But every gesture without a visible affordance is a feature only experts will discover — gesture power and discoverability trade off, and that tension never fully resolves.

5. **Motion is communication, not decoration** *(Modern Craft)* — Transitions carry state, hierarchy, and causality. Design motion *with* the layout, not as a final polish pass. Where something comes from and goes to is information.

6. **Navigation chrome is currently unsettled** — iOS 26 (Liquid Glass, floating tab bar) and Material 3 Expressive (nav drawer deprecated) are mid-transition. Treat platform navigation as in-flux; flag it, don't present a moving target as settled canon.

7. **Surface decision forks — don't default silently** — When a mobile design choice has multiple valid answers (nav pattern, sheet vs. modal, gesture vs. visible control), present 2-3 options with tradeoffs and let the user choose. See `references/design-forks.md`.

---

## The Designer Lenses

This skill reasons through eight named lenses rather than a flattened checklist. Each is a real practitioner with sharp, attributable positions. Weighting is **context-aware** — the lenses that matter most depend on what's being designed.

| Lens | Voice(s) | Leads when the work is about… |
|------|----------|-------------------------------|
| Ergonomic Realist | Steven Hoober | reach, action placement, grip assumptions |
| Thumb-Zone Mapper | Scott Hurff | layout cost, large-screen tradeoffs |
| Focus Forcer | Luke Wroblewski | content prioritization, forms |
| Direct Manipulation | Josh Clark | touch model, gesture vs. control, discoverability |
| Navigation Taxonomist | Frank Rausch | nav architecture, modals, drawers, sheets |
| Empirical Skeptic | Raluca Budiu / NN/g | scanning, overlays, myth-checking assumptions |
| Modern Craft | Allen / de With / Nelson | motion & feel, native-class craft, complex-app density |
| Platform Canon | Apple HIG / Material 3 | current conventions, system-reserved gestures |

Full positions, disagreements between lenses, and the weighting table are in `references/designer-lenses.md`. Load it at the start of both modes.

---

## Mode Selection

Two modes. Detect from `$ARGUMENTS` or ask.

### Detection

- `$ARGUMENTS` contains "audit", "review", "critique", "evaluate", "feedback" → **Audit**
- `$ARGUMENTS` contains "plan", "design", "build", "new", "how should" → **Plan**
- User points at an existing screen/component/app and asks for assessment → **Audit**
- User is starting a new mobile feature or unsure how it should work → **Plan**
- Ambiguous → Ask.

If AskUserQuestion is available:
- **Plan** — Design the mobile UX for a feature before any code is written
- **Audit** — Review an existing mobile design against best practice

Otherwise: "Do you want to (1) plan the mobile design for a new feature, or (2) audit an existing mobile design?"

If the user picks Audit but hasn't provided anything to review, ask what they're reviewing — a screenshot, a live URL, code, or a description — before routing. The audit workflow can work from any of these; it just needs one.

---

## Routing

| Mode | Read workflow | Load immediately |
|------|---------------|------------------|
| Plan | `workflows/plan.md` | `references/designer-lenses.md` |
| Audit | `workflows/audit.md` | `references/designer-lenses.md` |

Load other references on demand:
- `references/reach-and-ergonomics.md` — placing actions, thumb reach, one-handed use, large screens
- `references/navigation.md` — choosing nav patterns; tab bar, drawer, bottom sheet, modals
- `references/forms-and-input.md` — any screen with form fields or data entry
- `references/touch-gesture-motion.md` — gestures, touch targets, transitions, haptics, feel
- `references/content-and-attention.md` — content prioritization, scanning, glanceability, overlays
- `references/design-forks.md` — when a choice has multiple valid answers

---

## Gotchas — Where Claude Fails at Mobile Design

Mobile design failures are *judgment* failures, not code failures. Check every recommendation against this list.

1. **Defaulting to a hamburger menu because it looks "clean"** — Hidden navigation has poor discoverability. A tab bar exposes destinations and consistently outperforms it. The hamburger is a last resort for genuinely deep navigation, not a default.

2. **Primary action at the top of the screen** — A desktop habit. The top is the hardest thumb zone on a modern phone. Primary, frequent actions belong in the easy-reach lower band.

3. **Designing for the average grip** — There is no average grip. Designing only for comfortable one-handed reach penalizes the ~51% who are cradling or using two hands at any moment. Accommodate the most constrained grip.

4. **Treating a bottom sheet as a navigation destination** — Sheets are for transient, scoped tasks with the background still relevant. They are not pages. Never stack sheets to build a flow.

5. **Adding gestures with no visible affordance** — A swipe action nobody can see is a feature for experts only. Every gesture needs either a well-known convention behind it or a visible cue, and ideally a non-gesture fallback.

6. **Multi-column forms and placeholder-as-label** — Mobile forms are single-column, always. Placeholders that vanish on focus remove the label exactly when the user needs it. Labels stay visible.

7. **Treating motion as a final polish pass** — Motion that's bolted on after layout can't communicate where things come from. Decide transitions alongside the layout — they carry state and causality.

8. **Porting desktop information density to mobile** — Cramming a desktop screen's content into a phone is not mobile design. Mobile forces a prioritization decision: what is primary, what is deferred, what is cut.

9. **Designing for an unspecified "mobile" target** — Asked to design "for mobile," Claude silently defaults to iOS patterns (back-swipe, sheet detents, top-bar actions) that don't translate to Android. When the platform isn't stated, *name the assumption* and flag what differs across iOS / Android — and note where iOS 26 / Material 3 are mid-transition rather than treating either as settled.

10. **Optimizing for the showcase screen** — A design that looks great in a marketing shot may fail in one-handed use, with the keyboard open, mid-interruption, or for a returning user. Design for the real, distracted, glancing context.

For the reasoning behind each and the lens that catches it, see `references/designer-lenses.md` and the topic references.

---

## Reference Index

| File | Contents | Load when |
|------|----------|-----------|
| [designer-lenses.md](references/designer-lenses.md) | The 8 lenses — positions, disagreements, context-aware weighting table | Start of both modes |
| [reach-and-ergonomics.md](references/reach-and-ergonomics.md) | Grip data, thumb zones, action placement, large-screen reach | Placing actions or assessing reach |
| [navigation.md](references/navigation.md) | Rausch's nav taxonomy, tab/drawer/sheet/modal decisions, iOS 26 + Material 3 state | Choosing or reviewing navigation |
| [forms-and-input.md](references/forms-and-input.md) | Wroblewski's form principles, input types, field reduction, keyboard | Any data-entry screen |
| [touch-gesture-motion.md](references/touch-gesture-motion.md) | Touch targets, gesture discoverability, motion as communication, haptics | Gestures, transitions, or feel |
| [content-and-attention.md](references/content-and-attention.md) | Prioritization, scanning patterns, glanceability, overlay proliferation | Content layout and hierarchy |
| [design-forks.md](references/design-forks.md) | Ambiguous mobile decisions with options and tradeoffs | A choice has no single right answer |

## Workflow Index

| Workflow | Purpose |
|----------|---------|
| [plan.md](workflows/plan.md) | Discovery → prioritize → choose patterns → mobile design brief |
| [audit.md](workflows/audit.md) | Assess against lenses → surface forks → prioritized findings |
