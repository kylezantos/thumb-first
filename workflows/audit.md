# Workflow: Audit Mobile Design

Review an existing mobile design against the eight lenses, surface the forks that were decided silently, and return prioritized findings.

## Required Reading

- `references/designer-lenses.md` — load now; it drives the evaluation.
- Load topic references on demand per the routing table in SKILL.md.

---

## Step 1: Ingest What's Being Reviewed

Determine what the user has given you:
- A screenshot or image → assess what's visible; note what you can't judge (motion, states, real reach).
- A live URL or running app → if a browser tool is available, view it at a phone viewport; otherwise work from the user's description.
- Code → read the layout/structure, but judge the *design decisions*, not code quality (that's other skills).
- A description → work from it; ask for a screenshot if one would materially help.

**Scope it.** If the user pointed at one screen or one problem ("is the nav right?"), audit that — don't sweep the whole app. If they asked for a general review, cover the main screens.

Confirm scope and platform in one line before evaluating.

---

## Step 2: Establish Context

A few quick questions calibrate the audit (ask only what isn't obvious):
1. "What is this screen *for* — the one thing a user does here?"
2. "Who uses it, and in what situation?"
3. "Web, native, or both? Which platform should I weight?"

Without the primary purpose, you can't judge whether the design serves it — get it before evaluating.

---

## Step 3: Evaluate Through the Lenses

Walk the design through the lenses. Use the **weighting table** in `designer-lenses.md` — lead with the lenses that match what the screen is mostly about, but touch all eight. Load the topic reference for each as needed.

For each lens, ask its central question:

- **Ergonomic Realist** — does this work across grips? Is the primary action reachable? (`reach-and-ergonomics.md`)
- **Thumb-Zone Mapper** — what's in the high-cost zones — and should it be? (`reach-and-ergonomics.md`)
- **Focus Forcer** — was a real prioritization decision made, or is this a shrunk desktop screen? Forms? (`content-and-attention.md`, `forms-and-input.md`)
- **Direct Manipulation** — touch targets ≥44pt? Gestures with affordances? Chrome where content could be the control? (`touch-gesture-motion.md`)
- **Navigation Taxonomist** — does the nav pattern match the content structure? Overlays used correctly? (`navigation.md`)
- **Empirical Skeptic** — anything justified by trend not evidence? Overlay stacking? Heading structure for scanning? (`content-and-attention.md`)
- **Modern Craft** — does motion communicate or is it missing/decorative (Allen)? Does it *feel* genuinely native (de With)? If this is a dense or complex app, was it distilled for mobile or just shrunk (Nelson)? (load `touch-gesture-motion.md` before judging this row — its motion quality bars are needed)
- **Platform Canon** — convention conflicts? System-gesture collisions? In-transition iOS 26 / M3 dependencies? (`navigation.md`)

When you state a finding or judgment, **attribute it to the lens by name** — "Hoober's concern here…", "by Rausch's taxonomy this should be…", "Allen would push for…". For Modern Craft, name the sub-voice. Named reasoning is what makes the audit teachable rather than a list of opinions.

Also scan against the **Gotchas** list in SKILL.md — those are the highest-frequency failures.

---

## Step 4: Surface the Silent Forks

The most valuable part of the audit. For each major design choice, check `references/design-forks.md`: was this a genuine fork where another option might serve the user better?

For each silent fork found:
1. Name the decision the design made.
2. Show the alternative(s) and the tradeoff.
3. Give the signal for which is right *for this product* — or, if it depends on something only the user knows, frame it as a question.

A silently-decided fork isn't automatically wrong — but it should have been a *decision*. Surfacing it is the point.

---

## Step 5: Present Prioritized Findings

Two output modes — see `references/output-format.md` for the full spec.

- **HTML report (default).** Adapt `references/report-template.html` (a self-contained, worked example), fill it with this audit's content, write it to the audited project (`{project}/mobile-audits/{name}-mobile-audit.html` or the project's existing convention), and open it in the browser. The template governs the visual system: keep the four color channels separate, sample one accent from the product, and build the thumb-zone diagram with the numbered-dot + key-list pattern (never freehand SVG labels). Render only the sections this audit produced.
- **Terminal report (flag).** If the user passes `--terminal` / `--inline` / "skip the HTML" / "show it inline," skip the file and render the plain-text format below.

Both modes carry the same content, ordering, and lens attribution.

### Terminal format

```
MOBILE DESIGN AUDIT — [screen / feature]

Primary purpose: [the one thing]   Platform: [...]

Lens summary:
| Lens                 | Read                          |
| Ergonomic Realist    | [strong / concern / problem]  |
| Thumb-Zone Mapper    | [...]                         |
| Focus Forcer         | [...]                         |
| Direct Manipulation  | [...]                         |
| Navigation Taxonomist| [...]                         |
| Empirical Skeptic    | [...]                         |
| Modern Craft         | [...]                         |
| Platform Canon       | [...]                         |

Findings (priority order):

1. [Critical] [title]
   What: [the issue]   Lens: [which lens caught it]
   Why it matters: [user impact]
   Fix: [the design change — not the code]

2. [Important] [title]
   ...

3. [Polish] [title]
   ...

Silent forks worth revisiting:
- [decision made] → [alternative + when it'd be better] → [signal or question]

What I couldn't assess: [motion / states / real-device reach — if working from a static image]
```

Order findings by **user impact**: Critical (blocks or badly degrades the primary task) → Important (real friction) → Polish (refinement). Attribute each to its lens — that's what makes the audit teachable, not just a list.

**Wait gate:** present findings and stop. Ask before going further.

---

## Step 6: Offer Next Steps

After the user has read the findings, offer:
- **Re-plan a problem area** — switch to the Plan workflow for a screen that needs rethinking.
- **Hand off the fixes** — name the implementation skill (`responsive-craft`, `mobile-design-audit`, or the RN/Expo skills). thumb-first identifies the design changes; it doesn't write the implementation.

---

## Success Criteria

- [ ] Scope and primary purpose established before evaluating
- [ ] Design walked through all eight lenses, weighted to context
- [ ] Checked against the gotchas list
- [ ] Silent forks surfaced with alternatives and signals
- [ ] Findings prioritized by user impact and attributed to lenses
- [ ] Limits of the audit stated honestly (what couldn't be assessed)
- [ ] Next-step hand-off offered
