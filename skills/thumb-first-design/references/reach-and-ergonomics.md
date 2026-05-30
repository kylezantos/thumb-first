# Reach & Ergonomics

How people physically hold and touch phones, and what that means for where things go. Lenses: **Ergonomic Realist (Hoober)** and **Thumb-Zone Mapper (Hurff)**.

Load this when the work involves placing actions, assessing reach, one-handed use, or large-screen layout.

---

## How People Actually Hold Phones

Hoober's observational research (1,333 people, public settings):

| Grip | Share | Notes |
|------|-------|-------|
| One-handed | 49% | within this: 67% right thumb, 33% left |
| Cradled (two hands, one touches) | 36% | 72% touch with thumb, 28% with index finger |
| Two-handed (both thumbs) | 15% | mostly typing and gaming |

- **75% of all interactions are thumb-driven**, across every grip.
- **Portrait 90% of the time.**
- On large phones (6"+), two-handed use rises toward ~70% — but ~60% of interactions are *still* thumb-driven. The common large-phone grip: hold in one hand, tap with the other hand's index finger.

**The key correction:** these are population averages at a moment in time, not stable per-user states. The same person switches grips constantly — picking up a coffee, holding a rail, carrying a bag. **Never design for one grip.**

---

## The Thumb-Zone Map (Hurff)

A right-handed thumb sweeps an arc. That arc divides the screen into three zones:

```
┌─────────────────────────┐
│  Ow        Stretch      │   ← top: hardest to reach
│                         │
│  Stretch      Natural   │
│                         │
│  Natural    Natural     │   ← bottom: easiest (for that grip)
└─────────────────────────┘
   (right-handed, one-handed grip)
```

| Zone | Reach cost | Put here |
|------|-----------|----------|
| **Natural** | effortless | primary actions, main navigation, frequent controls |
| **Stretch** | conscious effort, grip may shift | secondary actions, settings, less-frequent controls |
| **"Ow"** | grip change or second hand | destructive actions, rarely-used controls — or nothing important |

**Use it as a cost map.** The zone tells you the *cost* of a location. A high cost is sometimes exactly what you want — "Delete Account" in the Ow zone is good design, because the reach friction is a safety margin. The mistake is putting the *primary* action there.

### Reach is non-linear with screen size

The map is not the same shape at every size — it shifts dramatically:

- **Small phones (≤4")** — thumb reaches almost everything; the Ow zone is tiny.
- **Standard (~6")** — Ow zone establishes itself across the entire top edge.
- **Large (6.7"+)** — the Ow zone is startlingly large. Hurff: the sheer amount of unreachable space "becomes startlingly apparent." Users "choke up" (slide the phone down in their hand) to compensate, which de-stabilizes the grip.

**Implication:** don't reuse one layout across phone size classes without re-checking reach. A layout tuned on a 6.1" simulator can put primary actions out of reach on a 6.9" device.

---

## Placement Rules

1. **Primary action → bottom band.** The most frequent, most important action goes in easy thumb reach — the lower third — *not* the top. A top-right "Save"/"Done"/"Post" is a desktop habit; it survives on iOS partly by convention, but a bottom-anchored primary action is more reachable. (See the fork in `design-forks.md`: top-bar action vs. bottom-anchored action.)

2. **Destructive action → high-cost zone, or behind confirmation.** Reach friction protects against mis-taps. "Delete," "Remove," "Sign out" belong in the Ow zone, behind a swipe, or behind a confirm step — never adjacent to a frequent action.

3. **Don't crowd the bottom edge.** The very bottom ~20pt collides with the home indicator and the system back/home gesture area. Keep tappable targets above it; respect the safe area as a *design* boundary, not just a technical one.

4. **Reachability is preferred, not guaranteed (Hoober's caution).** Because grips vary, "it's at the bottom so it's reachable" is an assumption, not a fact. For a *critical* action, also ensure it's reachable when cradled or two-handed — usually true if it's bottom-anchored and not jammed into a corner.

5. **Top of screen is for status and identity, not action.** Titles, context, read-only status — fine at the top. Frequent *interaction* up there fights the user's hand.

6. **Mind handedness asymmetry.** A bottom-*right* control is easy for the 67% right-thumbed and a stretch for the rest. Center-bottom or full-width bottom elements are the most grip-neutral. Reserve corners for secondary actions.

---

## One-Handed Use on Large Phones

Modern flagships are 6.7"+. The 2010-era "design for one-handed" advice predates them. Current synthesis:

- One-handed use of the *whole* screen is no longer realistic on large phones — accept it.
- Platforms responded in-product: iOS "Reachability" (swipe-down to pull the top half down), iOS 26 moving Search to a bottom-right island, Android keyboards offering one-handed mode.
- **Design response:** keep the *interaction surface* low. Content can fill the screen (you scroll content); controls, nav, and primary actions should cluster in the lower band. The top can hold scrollable content and non-interactive chrome.
- The "Reach Navigation" argument (Brad Ellis): a top navbar on a large phone demands a ~150% thumb-length increase to reach one-handed — which is why bottom tab bars, bottom sheets, and edge gestures exist. Treat bottom-anchored navigation as the default, top navbars as the exception.

---

## Quick Ergonomics Check

When placing or reviewing any interactive element, ask:

- [ ] Is the **primary** action in the bottom (Natural) band, reachable in the common grips?
- [ ] Are **destructive** actions in a high-cost zone or behind confirmation — not next to frequent ones?
- [ ] Does the layout still work for someone **cradling or two-handing** the phone, not just comfortable one-handed?
- [ ] Has reach been checked at the **largest** target device size, not just a mid-size simulator?
- [ ] Is the interaction surface kept **low**, with the top reserved for content and status?
- [ ] Are tappable targets clear of the **home-indicator / system-gesture** strip at the very bottom?
