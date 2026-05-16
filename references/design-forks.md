# Mobile Design Forks

Decisions where there is **no single correct answer** — the right choice depends on the product, the content, and the user's intent. When the skill hits one of these, it presents the options with tradeoffs and asks the user to choose. It does not default silently.

This is the core discipline of thumb-first: most mobile design failures aren't wrong *answers*, they're un-asked *questions*.

---

## How to Use This File

When planning or auditing, scan for the situations below. For each fork found:
1. Name the situation and why there's genuine ambiguity.
2. Present 2-3 options with honest tradeoffs.
3. Give the **signals** that point toward one option.
4. Ask the user to decide. If AskUserQuestion is available, present tappable options; otherwise, describe them and ask for a choice by letter.

Present forks **one or two at a time** — never a wall of decisions.

---

## Fork 1: Primary Navigation Pattern

**Situation:** the app has multiple top-level destinations and needs a navigation model.

- **A — Tab bar / bottom nav.** Destinations visible, thumb-reachable. Caps at ~5; can't express depth.
- **B — Hamburger / drawer.** Holds many destinations. Hidden → poor discoverability; Material 3 deprecated it.
- **C — Content-driven (no chrome).** Immersive, content *is* the navigation. Relies on gestures → discoverability cost.

**The question:** "How many genuinely top-level destinations are there, and how independent are they?"

**Signals:** 3-5 equal, independent sections → A. Deep hierarchy or 6+ destinations → fix the IA first, then A with a "More" tab, or B as last resort. Single-purpose immersive app (camera, reader, player) → C.

---

## Fork 2: Sheet vs. Modal vs. Full-Screen vs. New Screen

**Situation:** a secondary task or content needs to appear over or after the current screen.

- **A — Bottom sheet.** Quick, scoped, dismissible. Non-modal if background stays relevant.
- **B — Full-screen modal.** A self-contained task the user must finish or cancel.
- **C — New pushed screen.** Part of the main navigation flow; the user drilled in.
- **D — Inline expansion / state change.** No new surface at all — the current screen changes.

**The question:** "Is this transient (a quick detour) or a real destination — and does the user need the background?"

**Signals:** brief task, background still relevant → A non-modal. Brief task, background irrelevant → A modal. Substantial self-contained task → B. Part of the content hierarchy → C. Just revealing more of what's already here → D. **If it's a multi-step flow, it is never a stack of sheets** — it's C or B.

---

## Fork 3: Gesture vs. Visible Control

**Situation:** an action could be a swipe/long-press/drag, or a visible button.

- **A — Gesture only.** Fast, clean, uncluttered. Invisible — experts only.
- **B — Visible control only.** Discoverable by everyone. Adds chrome; less expressive.
- **C — Both.** Gesture as accelerator, visible control as the discoverable path.

**The question:** "Is this action primary (everyone must find it) or an accelerator (power users will seek it)?"

**Signals:** primary action → B or C, never A alone. Frequent action for return users → C. Secondary accelerator on a well-known convention (swipe-to-delete) → A acceptable. Novel gesture → never A alone; needs an affordance or fallback.

---

## Fork 4: Primary Action Placement

**Situation:** a screen has one clear primary action — where does it live?

- **A — Top bar (e.g. top-right "Done"/"Save").** Platform-conventional on iOS; out of easy thumb reach.
- **B — Bottom-anchored button.** In the Natural reach zone; costs vertical space; can overlap content/keyboard.
- **C — Floating action button.** Persistent, reachable, one action; can occlude content; a strong Material idiom, less so on iOS.

**The question:** "How often is this action used, and how much does platform convention matter here?"

**Signals:** frequent action → B or C (reach wins). Infrequent confirm on an iOS form where convention is strong → A is acceptable. One dominant create action → C. Action tied to a scrolling list → B as a docked bar. Destructive action → never the easy-reach primary slot (see Fork 8).

---

## Fork 5: List vs. Grid vs. Carousel

**Situation:** a collection of items needs to be displayed.

- **A — Vertical list.** Best scan for text-primary items; variable density; one column.
- **B — Grid.** Best for visual/image-primary items of equal weight; enables visual comparison; less room for metadata.
- **C — Horizontal carousel.** Compact, shows a category in little vertical space; off-screen items are easily missed.

**The question:** "Are users scanning text/metadata, comparing images, or browsing a peripheral category?"

**Signals:** text and metadata matter → A. Images are the content and items are comparable (photos, products) → B. A secondary category alongside other content → C — but never put *primary* content in a carousel; off-screen = unseen.

---

## Fork 6: Long Form — One Scroll vs. Steps vs. Sections

**Situation:** a form has many fields.

- **A — Single long scroll.** Everything visible; user sees the whole commitment; can feel endless.
- **B — Stepped wizard.** One group per screen; reduces per-screen load; needs a progress indicator; user can't see the whole.
- **C — Accordion sections.** Collapsible groups on one screen; good for edit-specific-thing; risk of missed required fields in collapsed sections.

**The question:** "Is this fill-once (signup, checkout) or edit-often (settings, profile)?"

**Signals:** fill-once with 10+ fields or natural stages → B. Fill-once and short → A. Edit-often, users touch one section at a time → C. Mixed required/optional → step through required (B), accordion the optional.

---

## Fork 7: Information Density — Show All vs. Progressive Disclosure

**Situation:** a screen *could* show a lot of information.

- **A — Show it all.** Everything at a glance; no extra taps; risks clutter and a lost focal point.
- **B — Progressive disclosure.** Show the essential; reveal detail on demand; cleaner; costs a tap and risks "I didn't know that was there."

**The question:** "Does the user need all of this at once, or do they need the summary with detail on demand?"

**Signals:** monitoring/at-a-glance use (dashboard, status) → A, but with clear hierarchy and one focal point. Task-focused use → B — show what the task needs, defer the rest. Power-user tool used daily → A leans better (familiarity beats tidiness). New-user or occasional-use product → B.

---

## Fork 8: Destructive & Rare Actions — Where Do They Hide?

**Situation:** a screen has both frequent actions and destructive/rare ones.

- **A — High-cost zone.** Place destructive actions in the "Ow" zone — reach friction is a safety margin.
- **B — Behind a swipe or overflow menu.** Out of the way until sought; relies on discoverability (fine — they're rare).
- **C — Behind a confirmation step.** Reachable, but a deliberate second action guards it.

**The question:** "How damaging is a mis-tap, and how reversible is the action?"

**Signals:** irreversible and damaging (delete account, wipe data) → C, and ideally also A. Reversible but annoying (archive, remove from list) → B with an undo. Rare but harmless → B. **Never** place a destructive action adjacent to a frequent one in the same easy-reach zone.

---

## Fork 9: Onboarding the Interface

**Situation:** the app has patterns or gestures a new user must learn.

- **A — Upfront tour.** Carousel/coach-marks on first launch; users skip and forget it.
- **B — Contextual reveal.** Teach each thing at the moment it becomes relevant; better retention; more design work per moment.
- **C — No teaching.** Rely entirely on convention and affordance; only works if the design is genuinely self-evident.

**The question:** "Can the interface teach itself through affordances, or are there genuinely non-obvious moments?"

**Signals:** design uses only conventions and visible affordances → C (the goal). A few non-obvious accelerators → B. Upfront tour (A) is almost always the wrong answer — if you need one, treat it as a signal the design isn't self-evident yet.
