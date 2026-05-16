# Touch, Gesture & Motion

The interaction layer — how the user touches the interface and how it moves in response. Lenses: **Direct Manipulation (Clark)**, **Modern Craft (Allen / de With / Nelson)**, **Platform Canon**.

Load this when designing gestures, touch targets, transitions, or anything about how the interface *feels*.

---

## Touch Targets

The non-negotiable floor:

| Platform | Minimum target | Spacing |
|----------|---------------|---------|
| Apple HIG | 44 × 44 pt | "don't risk activating an adjacent control" (no number) |
| Material 3 | 48 × 48 dp | 8 dp minimum between targets |
| WCAG 2.5.8 (AA) | 24 × 24 px (floor) | — |
| WCAG 2.5.5 (AAA) | 44 × 44 px | — |

**Design to 44pt minimum, 48dp preferred.** Clark's framing: 44pt is "the basic unit" — derive spacing and rhythm *from* it, don't treat it as a constraint to squeeze under.

**Visual size and touch size are separate.** A 24pt icon is fine visually — its *hit area* must still be ≥44pt, expanded with transparent padding. The user touches the target they can't see; the icon they can see just has to be findable.

**Spacing matters as much as size.** Two 44pt targets jammed together still cause mis-taps. Keep ≥8dp between adjacent targets; more between a frequent action and a destructive one.

---

## Gestures and the Discoverability Tension

Clark's most honest position, and the one this skill must never paper over: **gestures are the keyboard shortcuts of touch.** Faster and more expressive for the user who knows them — and completely invisible to the user who doesn't. This tension never fully resolves. Manage it; don't pretend it's solved.

### The rule

**Every gesture needs one of these, or it doesn't ship as the only path:**
1. It's a **near-universal convention** (swipe-to-go-back, pull-to-refresh, swipe-to-delete a list row, tap, scroll) — users already have it, or
2. It has a **visible affordance** — a peeking element, a handle, a hint of the action underneath, or
3. It has a **non-gesture fallback** — the same action is also reachable via a visible control.

A novel gesture with none of the three is a feature for experts only. That's a legitimate design choice for a *secondary* accelerator — never for a *primary* action.

### Teaching gestures

Don't front-load a gesture tutorial at install — users skip it and forget it. **Teach at the moment of need:** reveal the hint exactly when the user is in the situation the gesture serves (the canonical example: a swipe-up hint appearing the first time a video is paused). Contextual reveal beats upfront onboarding.

### System-reserved gestures — hands off

| Gesture | iOS | Android |
|---------|-----|---------|
| Bottom edge swipe up | Home / app switcher | Home; hold = Recents |
| Top edge swipe down | Notification / Control Center | Notification shade |
| Side / left edge swipe | Back (iOS 26: anywhere on screen) | System Predictive Back |

Don't bind app actions to these. If an app gesture sits near a system edge, expect conflict — design the app gesture inboard, and never make an edge-conflicting gesture the only way to do something.

### Swipe actions on rows — the platform convention

Leading edge → positive/neutral action (archive, mark read). Trailing edge → negative/destructive (delete). Honor the convention; reversing it surprises users. Always pair a row swipe with a non-swipe path (a tap-through detail with the same actions) — the swipe is invisible.

---

## Motion as Communication

The Modern Craft lens's core position (Allen most sharply): **motion is not polish applied at the end — it is how the interface communicates state, hierarchy, and causality.** Design transitions *with* the layout.

### What motion is *for* (the working tests)

- **Origin and destination** — where did this come from, where did it go? A sheet that rises from a button ties the two together. A screen that pushes in from the right says "deeper"; sliding it back says "you returned." Spatial consistency builds the user's mental map.
- **Continuity** — animate the *change between states*, don't cut. A cut forces the user to re-find everything; a transition carries their attention across.
- **Causality** — motion confirms "your tap did this." Immediate, direct response to touch (the element tracks the finger, reacts on contact) makes the interface feel physical and trustworthy.
- **Status** — loading, progress, success. Motion communicates ongoing activity more naturally than static text.

### What motion is *not* for

Decoration. An animation that doesn't tell the user something — that exists to look lively — adds latency and noise. If you can't name what a transition *communicates*, cut it.

### Quality bars

- **Speed is a design property** (de With — "light, native, fast"). Slow is a design failure, not only an engineering one. UI transitions should feel instant — roughly 200-300ms for most; long enough to follow, short enough to never wait on.
- **Interruptible.** A user who taps again mid-animation should be obeyed immediately. Motion must never hold the interface hostage.
- **Honor the physical metaphor (Clark).** If something looks grabbable, it must drag. If it looks like a card, it must move like one. A half-honored metaphor (looks physical, behaves like a static button) is worse than no metaphor.
- **Respect reduced-motion.** When the OS signals reduced motion, replace large positional/scale animation with a simple fade or an instant change. The *information* the motion carried must still arrive — just without the movement.

### Haptics

Haptics are touch feedback — design them, don't leave them to defaults.

- **iOS** vocabulary is *semantic*: notification haptics (`success` / `warning` / `error`) and impact haptics (`light` → `heavy`). Use the named one that matches the meaning — a `success` haptic on a failure miscommunicates to users who rely on feel.
- **Android** vocabulary is *intensity-based* (`TICK` → `HEAVY_CLICK`); the designer maps intensity to emotional weight — lighter for confirmations, stronger for errors/rejections.
- **Every haptic pairs with a visible change**, and haptics lose meaning when overused. A buzz on every tap is noise; a buzz at a meaningful moment is signal.

---

## Touch / Gesture / Motion Review Checklist

- [ ] Are all touch targets **≥44pt / 48dp**, with hit areas expanded beyond small icons?
- [ ] Is there **≥8dp spacing** between adjacent targets, more around destructive ones?
- [ ] Does every gesture have a **convention, a visible affordance, or a fallback** — or is it a primary action with none (a defect)?
- [ ] Are gestures **taught at the moment of need**, not in an upfront tutorial?
- [ ] Do any app gestures **collide with system-reserved** edges?
- [ ] Do row swipes follow the **leading=positive / trailing=destructive** convention and have a non-swipe path?
- [ ] Does each transition **communicate** something nameable (origin, continuity, causality, status) — or is it decoration to cut?
- [ ] Do transitions feel **fast (~200-300ms) and interruptible**?
- [ ] Is there a **reduced-motion** path that still delivers the information?
- [ ] Are haptics **meaningful, semantically correct, and paired with a visible change** — not constant noise?
