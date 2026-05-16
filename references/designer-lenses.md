# Designer Lenses

Thumb-first reasons through eight named lenses, not a flattened checklist. Each is a real practitioner (or institution) with sharp, attributable positions. This file holds the positions, the disagreements between lenses, and the weighting table.

**Why lenses, not rules:** mobile design has few universal rights and many context-dependent tradeoffs. A lens carries a *point of view* — it tells you what that designer would push back on. Reasoning through several lenses surfaces tension a checklist would flatten.

---

## How to Use This File

1. At the start of Plan or Audit mode, read this file.
2. Identify what the work is about (navigation? forms? reach? motion?).
3. Use the **weighting table** at the bottom to decide which 2-3 lenses lead.
4. When lenses disagree (see "Where the Lenses Disagree"), surface the disagreement to the user as a design fork — don't silently pick one.
5. Attribute. Say "Hoober's point here is…" — named reasoning is more useful and more honest than anonymous "best practice."

---

## Lens 1: The Ergonomic Realist — Steven Hoober

Hoober ran street-level observational research (1,333 observations) on how people actually hold phones.

**The data:** 49% one-handed, 36% cradled (two hands, one touching), 15% two-handed. 75% of all interactions are thumb-driven. Portrait 90% of the time. On large phones, two-handed use rises to ~70% but interactions stay ~60% thumb-driven.

**His load-bearing position — and the one most often misapplied:**
> "Always accommodate the most constrained grip, so people can use your interface no matter how they choose to hold their device."

The 49% one-handed figure is **not** permission to assume one-handed use. Grips switch "every few seconds" with the task. Design so the interface works in the *hardest* grip the user might be in.

**What this lens catches:** layouts that assume a fixed grip; primary actions placed where only a comfortable one-handed reach finds them; "mobile = on-the-go and one-handed" oversimplification.

**Dated vs. durable:** the exact 49/36/15 split is 2013 pre-6" data — directional, not current. The principle (design for the constrained grip) is a risk-management rule and fully durable.

---

## Lens 2: The Thumb-Zone Mapper — Scott Hurff

Hurff turned grip research into a placement framework: the screen has three reach zones.

| Zone | Meaning | Belongs here |
|------|---------|--------------|
| **Natural** (green) | effortless thumb arc | primary actions, main nav, frequent controls |
| **Stretch** (yellow) | reachable with conscious effort, grip may shift | secondary actions, settings |
| **"Ow"** (red) | hard to reach, needs a grip change or second hand | destructive/rare actions — or nothing important |

**His sharp position:** designers were treating big screens as scaled-up small screens. The ergonomic map is **non-linear** with screen size — the "Ow" zone explodes on large phones; even a "stretch" causes an unconscious grip re-anchor that slows the task and raises drop risk. Re-map per device class.

**Use it as a cost map, not a grid.** The zones tell you the *cost* of a location. Putting "Delete" in the Ow zone is correct — the cost is a feature. Putting the main CTA there is the mistake.

**What this lens catches:** primary actions in high-cost zones; the same layout reused across phone sizes without re-mapping; treating top-of-screen as neutral.

---

## Lens 3: The Focus Forcer — Luke Wroblewski

Wroblewski's *Mobile First* argued the small screen is an asset, not a constraint to apologize for.

**Core position:** designing mobile-first "forces you to focus and enables you to innovate." The screen makes you answer "what actually earns this space?" — and that answer is the right priority on *every* screen. Prioritization is the design work; layout is downstream of it.

**On forms (still the definitive source):**
- **Single column, always** — no exceptions on mobile.
- **Labels stay visible** — top-aligned; placeholder-as-label is an anti-pattern because the label vanishes exactly when the user is entering data.
- **Match input type to data type** — email field → email keyboard; not doing so is an avoidable tax.
- **Aggressive field reduction** — for every field ask "what happens if we remove this?" Smart, adjustable defaults make users measurably faster.

**What this lens catches:** desktop content crammed onto a phone with no prioritization decision; multi-column forms; placeholder labels; forms that ask for more than they need.

**Dated vs. durable:** the 2011 "mobile is exploding" argument is now just reality. The focus principle and the form rules are structural and durable.

---

## Lens 4: Direct Manipulation — Josh Clark

Clark's *Tapworthy* and *Designing for Touch* argued touch demands rethinking interaction, not shrinking desktop UI.

**Core positions:**
- **"Buttons are a hack"** — buttons are an abstraction invented for the mouse. On touch, prefer manipulating content directly: "Who needs a control when you have the content itself?" Swipe the card, flick the list.
- **44pt is the basic unit** — the minimum reliably tappable target, anatomical not pixel-based. Layout and spacing should derive *from* it.
- **Gestures are the keyboard shortcuts of touch** — faster and more expressive, and invisible to the novice in exactly the same way.
- **Honor physical metaphors completely** — if something looks like a physical object, people will try to interact with it like one. Half-honored metaphors fail.

**The discoverability tension — his most honest position:** he does *not* resolve gesture-vs-button cleanly. Hidden gestures cost discoverability; visible buttons cost expressiveness and feel un-native. His heuristic: teach gestures contextually, at the moment of need — not in an upfront tutorial.

**What this lens catches:** chrome-heavy designs that wrap controls around content instead of making content interactive; gestures with no affordance; sub-44pt targets; skeuomorphic cues that don't behave as they look.

---

## Lens 5: The Navigation Taxonomist — Frank Rausch

Rausch maintains the most rigorous current taxonomy of mobile navigation.

**The taxonomy:**
- **Structural** — Drill-Down, Flat (tabs), Pyramid, Hub-and-Spoke
- **Overlay** — High-Friction Modal (requires a decision), Low-Friction Modal (swipe-dismissible), Non-Modal (doesn't block)
- **Embedded** — State Change, Step-by-Step, Content-Driven

**Sharp positions:**
- Hamburger menus are "the tab bar's evil sibling" — poor discoverability; tab bars measurably outperform them.
- Single-action alert dialogs should usually be replaced with inline text.
- Full-screen transitions are wrong for an in-place state change.
- Standard screen-edge pan (back) should be baseline support, not an enhancement.

**What this lens catches:** navigation chosen by aesthetics rather than content structure; modals used where a state change belongs; flows built by stacking overlays.

---

## Lens 6: The Empirical Skeptic — Raluca Budiu / NN/g

Budiu leads NN/g's mobile research — the lens that checks fashionable assumptions against evidence.

**Findings that overturn older canon:**
- **Platform convergence is real** — iOS and Android have converged enough that near-identical UIs work on both. "Design separately per platform" is largely obsolete (NN/g, 2023).
- **Forms are no longer the top friction** — passkeys, magic links, biometrics, autofill fixed most of it. The new dominant problem is **overlay proliferation** — layered sheets/popups causing accidental dismissal and disorientation — plus in-app-browser chrome conflicts.
- **Scanning patterns** — F-Pattern (low comprehension), Spotted (jumps to bold/links/numbers), Layer-Cake (headings only — the most effective for navigation), Commitment (full reading — needs trust/motivation). On mobile, the "marking" pattern (eyes fixed, thumb scrolls) is common — so **heading/subheading structure matters more than left-edge alignment**.
- **Bottom sheets (2024 guidance)** — the bottom of the screen is *not* automatically in thumb reach (grips vary). Sheets are validated for transient tasks with persistent background context — **not** as primary navigation, and **never stacked**.

**What this lens catches:** designs justified by trend rather than evidence; overlay-stacking; assuming bottom-of-screen = reachable; weak heading structure.

---

## Lens 7: Modern Craft — Andy Allen / Sebastiaan de With / Gavin Nelson

The contemporary voices — three practitioners shipping the apps the field currently treats as benchmark mobile craft. They are distinct people with distinct authority. Invoke the one whose domain matches the work, **by name** — don't blur them into a generic "craft" voice.

### Andy Allen — expressive feel (Not Boring Software)
Maximalist, sensory interaction. Positions:
- **Minimalism is a place to visit, not to live.** Allen rejects minimalism as a governing philosophy in favor of "richness and texture and fun."
- **The screen subtracts; the designer adds it back.** "Experiences on our phones are still trapped behind a screen, so you have to put in what's taken away" — deliberate haptic, audio, and dimensional feedback at the moments conventional design leaves silent.
- **Game feedback loops belong in utility apps** — challenge, reward, and response aren't only for games; a habit tracker can use them.
- **Leads when:** the work is about how an interaction *feels* — sensory feedback, haptics, motion personality, making a screen feel alive.

### Sebastiaan de With — native-class craft (Halide; joined Apple's Human Interface Design team, 2026)
Restraint and native fidelity. Positions:
- **Framing decides everything downstream.** "We didn't say we made an app. We say we made a camera." What you decide the thing fundamentally *is* governs every later choice.
- **The pro-tool tension:** offer "ultimate control and flexibility while keeping the experience simple and accessible." Depth must never cost approachability.
- **Light, native, fast — as design qualities.** Responsiveness and a lightweight feel are design outcomes, not just engineering ones.
- **Leads when:** the work is about native-class fidelity, pro/power-tool interaction models, performance-as-design, or what the product fundamentally is.

### Gavin Nelson — complexity made native (Linear Mobile; now OpenAI)
Information density on a touch surface. Positions:
- **Distill, don't shrink.** Mobile means "distilling complexity even further" — making desktop-scale density genuinely accessible on touch, not cramming a desktop screen onto a phone.
- **The bar is "a natural extension of oneself."** Good mobile design feels like something you never had to learn — pull-to-refresh and AirPods auto-pause are the standard.
- **Design from a vocabulary.** Nelson works from a large curated reference library and long stretches away from the screen before tooling — pattern fluency precedes execution.
- **Leads when:** the work is about porting complex or dense software to mobile, information architecture, or complexity reduction.

**The internal tension — surface it, don't average it.** Allen (maximalist, expressive) and de With (restrained, native-quiet) genuinely disagree on how much personality an interface should carry. That's a fork, not a contradiction — see "Where the Lenses Disagree."

**What this lens catches:** motion added last and therefore meaningless; transitions that don't say where things went; janky or slow interactions excused as "an engineering problem"; a dense desktop screen shrunk rather than re-thought; an interface with no personality where the product wants one — or a noisy one where the product wants quiet.

---

## Lens 8: Platform Canon — Apple HIG / Material 3

The institutional conventions. Not a person, but a lens: "what does the platform expect, and is the platform itself currently stable here?"

**Stable canon:** touch-target minimums (Apple 44pt / Material 48dp, with 8dp spacing on Material); system-reserved gestures (home, back, notification/control surfaces); tab bars for 3-5 equal top-level destinations; sheet/modal/alert distinctions.

**Currently unsettled — flag, don't canonize:**
- **iOS 26 (Liquid Glass)** — floating, scroll-collapsing tab bar; a new "accessory" layer; back-swipe now works anywhere on screen. NN/g published a critical usability assessment; treat as in-transition.
- **Material 3 Expressive (2025)** — navigation drawer **deprecated**, tall nav bar replaced by a shorter "flexible navigation bar." No clean phone replacement for 5+ destinations — an acknowledged gap.

**What this lens catches:** cross-platform convention mixing; intercepting system-reserved gestures; presenting a mid-transition pattern as settled.

---

## Where the Lenses Disagree

Real tension — surface these as design forks rather than resolving them silently.

- **Hoober vs. Hurff on placement.** Hurff's zones invite "put primary actions in the bottom Natural zone." Hoober cautions that ~51% aren't in the grip that makes that zone easy. *Resolution:* use zones as a cost map; validate against grip diversity; bottom is *preferred*, not *guaranteed*.
- **Clark vs. the ergonomics lenses.** Hoober/Hurff care where the *control* sits. Clark argues that when content *is* the control, the placement question partly dissolves. *Resolution:* both apply at different layers — interaction model first (Clark), then placement of whatever chrome remains (Hoober/Hurff).
- **Allen vs. de With on expressiveness.** Within Modern Craft itself: Allen argues interfaces should carry rich sensory personality; de With argues for light, quiet, native restraint. *Resolution:* it's a product-type fork — a utility, habit, or consumer-delight app can carry Allen's expressiveness; a pro tool, productivity, or information-dense app leans de With's and Nelson's restraint. Decide it deliberately; don't split the difference into something characterless.
- **Modern Craft vs. Empirical Skeptic.** The craft voices push expressive motion and novel interaction; Budiu warns novelty often fails usability testing (see iOS 26 reception). *Resolution:* expressive *and* discoverable — motion must communicate, not just decorate; novel gestures need affordances.
- **"Bottom sheet everywhere" vs. NN/g.** Contemporary practice over-uses sheets as navigation; NN/g restricts them to transient tasks. *Resolution:* sheets for scoped, dismissible tasks with relevant background — never as pages, never stacked.

---

## Context-Aware Weighting Table

Which lenses lead, by what's being designed or reviewed:

| The work is about… | Lead lenses | Supporting |
|--------------------|-------------|------------|
| Where actions/buttons go | Hoober, Hurff | Platform Canon |
| Navigation structure | Rausch, Platform Canon | Budiu |
| A form or data entry | Wroblewski | Clark, Platform Canon |
| Gestures / touch interaction | Clark, Platform Canon | Modern Craft → Allen |
| Transitions / motion / "feel" | Modern Craft → Allen | Clark |
| Content layout & hierarchy | Wroblewski, Budiu | Hurff |
| Bottom sheet / modal decision | Rausch, Budiu | Platform Canon |
| Large-screen / one-handed concerns | Hurff, Hoober | — |
| Native-class quality / "is this good?" | Modern Craft → de With, Platform Canon | Budiu |
| Porting a complex/dense app to mobile | Modern Craft → Nelson, Wroblewski | Budiu |
| Validating a trendy choice | Budiu | Rausch |

For the **Modern Craft** lens, the arrow names which of the three sub-voices leads: Allen for feel and expressiveness, de With for native-class quality, Nelson for complexity and information density.

When in doubt, run the two lead lenses plus Budiu — the Empirical Skeptic is the cheapest guard against confident-but-wrong mobile advice.
