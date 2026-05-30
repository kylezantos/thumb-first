# Navigation

Choosing and reviewing mobile navigation — the single richest source of decision forks in mobile design. Lenses: **Navigation Taxonomist (Rausch)**, **Platform Canon (Apple HIG / Material 3)**, **Empirical Skeptic (Budiu)**.

Load this when designing or reviewing how a user moves between and within screens.

---

## Rausch's Navigation Taxonomy

Pick navigation by **content structure**, not by aesthetics. Three families:

### Structural — how top-level content is organized
| Pattern | Shape | Use when |
|---------|-------|----------|
| **Flat (tabs)** | 3-5 peer sections, switch freely | sections are independent and roughly equal — Music, App Store, most consumer apps |
| **Drill-Down** | push/pop hierarchy | parent→child content — Settings, Mail, file browsers |
| **Hub-and-Spoke** | a home base you return to between tasks | distinct tasks launched from a center — older iOS home screen model |
| **Pyramid** | siblings reachable from within a detail view | browsing a sequence — next/previous photo, article |

### Overlay — temporary content over the current screen
| Pattern | Blocks? | Use when |
|---------|---------|----------|
| **High-friction modal** | yes, requires a decision | the user must choose or finish before continuing |
| **Low-friction modal** | yes, but swipe-dismissible | a scoped task; escape is cheap |
| **Non-modal** | no, background stays live | the overlay relates to still-relevant background (filter over a map) |

### Embedded — navigation inside a screen
State Change, Step-by-Step (wizard), Content-Driven (the content itself is the path).

**Rausch's positions:** hamburger menus have poor discoverability ("the tab bar's evil sibling"); single-action alert dialogs should usually become inline text; a full-screen transition is wrong for an in-place state change; edge-swipe back should be baseline.

---

## The Primary Navigation Decision

The most common fork. Options and when each wins:

### Tab bar / bottom navigation
- **Best for:** 3-5 independent, roughly-equal top-level destinations.
- **Why it wins:** destinations are *visible* — discoverability is built in. Bottom placement is thumb-reachable. Both platforms make it the default.
- **Limits:** hard ceiling of ~5 items; cannot express hierarchy depth; tabs must be *stable* (not role- or experiment-dependent); tabs are for *navigation between modes*, not for actions like "Create" or "Scan."

### Hamburger / drawer
- **Best for:** genuinely deep or sprawling navigation that can't fit 5 tabs — and even then, reluctantly.
- **Why it's a last resort:** hidden behind one icon → poor discoverability → measurably lower engagement with buried destinations. Material 3 Expressive has **deprecated the navigation drawer**.
- **If you must:** pair it with a tab bar for the top 3-5, and use the drawer only for the long tail.

### Content-driven / no chrome
- **Best for:** immersive, single-purpose apps (a camera, a reader, a game) where content *is* the navigation.
- **Cost:** relies on gestures — carries the discoverability tax (see `touch-gesture-motion.md`).

> **Claude's default failure:** reaching for a hamburger because it looks tidy. Visible-but-busy beats tidy-but-hidden. Start from a tab bar; justify any move away from it.

### The 5+ destinations problem
If you have more than 5 top-level destinations, that is itself a finding — surface it. Options: (a) a 5th "More" tab, (b) reduce to true top-level destinations and demote the rest into sections, (c) drawer as last resort. Material 3 Expressive deprecating the drawer leaves *no clean phone answer* for 5+ — which is a signal the information architecture, not the nav component, needs the work.

---

## Sheets vs. Modals vs. Full-Screen

Bottom sheets are the most over-used pattern in current mobile design. Decide deliberately.

| Surface | Background | Use when | Don't |
|---------|-----------|----------|-------|
| **Bottom sheet (modal)** | scrim, blocked | a scoped, brief task; a focused choice; replaces a menu/simple dialog | use as a page; stack two |
| **Bottom sheet (non-modal / standard)** | live, interactive | the task relates to still-relevant background — filters over a map, a player over a list | use when background is irrelevant |
| **Full-screen modal** | fully covered | a self-contained task the user must finish or explicitly cancel — compose, onboarding | use for something quick |
| **Alert / dialog** | blocked | critical info needing an immediate decision; 2 buttons max | use for non-critical info, or single-button "OK" noise |
| **Popover** | live | iPad/desktop only — on phones it becomes a sheet | design phone-specific popovers |

**The deciding question is interaction-blocking, not size:** does the user need to see/use the background? Yes → non-modal. No, and they must resolve this first → modal/full-screen.

**NN/g's hard rules for sheets (2024):**
- Sheets are for **transient** tasks, not destinations. A sheet is not a page.
- **Never stack sheets** to build a flow — it destroys the user's model of where they are.
- Dismissal must be obvious. Swipe-down conflicts with system gestures; always also give a visible close affordance.
- "The bottom of the screen is reachable" is not guaranteed — grips vary (Hoober). Don't justify a sheet purely by reachability.

**Apple's detents** give sheets explicit sizes: `.medium` (~half screen, auxiliary tasks/quick reference) and `.large` (full, substantial content/input). Material's sheets have no size tiers — only the standard/modal (scrim) distinction.

---

## Platform Canon — Current State (flag as in-transition)

### Stable
- **Tab bars:** 3-5 items, both platforms, primary phone navigation.
- **System-reserved gestures:** bottom-edge = home/recents; top-edge = notification/control surfaces; edge-swipe = back. Apps must not intercept these.
- **Back behavior:** iOS = left-edge (iOS 26: anywhere-on-screen) swipe within a nav stack. Android = system Predictive Back, which shows a preview of the destination — apps must support it and must not hijack it for internal routing.

### Unsettled — name it, don't canonize it
- **iOS 26 (Liquid Glass):** tab bar is now a floating, semi-transparent capsule that collapses on scroll; a new "accessory" layer sits above it for persistent global UI (mini-player, live status). NN/g published a critical usability review. If designing for iOS 26, treat the floating tab bar as current but contested.
- **Material 3 Expressive (2025):** nav drawer deprecated; the tall navigation bar replaced by a shorter "flexible navigation bar"; on larger screens, navigation rail replaces the drawer. No defined phone replacement for 5+ destinations.

**Rule:** when a recommendation depends on iOS-26- or M3-Expressive-era chrome, say so explicitly and note it's a moving target. Don't present a pattern in active transition as settled best practice.

---

## Navigation Review Checklist

- [ ] Is the navigation pattern chosen from **content structure**, not aesthetics?
- [ ] Are top-level destinations **visible** (tabs) rather than hidden (drawer) unless depth truly forces it?
- [ ] Are there **>5 top-level destinations**? If so, is that flagged as an IA problem?
- [ ] Are sheets used for **transient tasks only** — never as pages, never stacked?
- [ ] Is every modal's blocking behavior **deliberate** (does the user need the background or not)?
- [ ] Do dismissal affordances **not rely on gesture alone**?
- [ ] Are **system-reserved gestures** left untouched, and platform-appropriate (iOS vs. Android back)?
- [ ] If the design leans on **iOS 26 / M3 Expressive** chrome, is that called out as in-transition?
