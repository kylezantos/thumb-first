# Workflow: Plan Mobile Design

Design a feature's mobile UX *before* code — decide patterns, ergonomics, and content priority, then hand off a brief an implementer can build from.

## Required Reading

- `references/designer-lenses.md` — load now; it drives every step.
- Load other references on demand per the routing table in SKILL.md.

---

## Step 1: Discovery

Understand the feature before designing it. Ask **2-3 questions at a time**, not a wall.

If AskUserQuestion is available, present tappable options; otherwise ask in plain text.

**Round 1 — what and who:**
1. "What is the feature, and what is the *one* thing a user comes to it to do?"
2. "Web, native (iOS/Android), or both? Any platform that matters most?"
3. "New screen(s), or a mobile version of something that exists on desktop?"

**Round 2 — context (ask only what's still unknown):**
4. "Who uses it, and in what situation — focused and seated, or glancing mid-task?"
5. "First-time users, returning power users, or both?"
6. "Any fixed constraints — an existing design system, brand, platform requirement?"

If the user gave a lot up front, skip questions already answered. Confirm your understanding in 2-3 lines before moving on.

---

## Step 2: Prioritize Content

Before any layout, do the prioritization work (Focus Forcer lens — see `references/content-and-attention.md`).

1. List everything the feature could show or do.
2. Rank: what is the screen's single primary purpose? Then secondary, then the rest.
3. Decide for each lower item: keep / defer (behind a tap or lower on scroll) / cut.

Present the ranked list and the cut/defer calls — attribute the reasoning to the lens by name ("Wroblewski's point: the screen forces the question *what earns this space?*"). Named reasoning is the skill's value; don't present this as anonymous best practice. **Wait gate:** the user confirms priorities before pattern decisions — layout is downstream of this.

---

## Step 3: Choose Patterns — Surface the Forks

Now decide the structure. For each major decision, check `references/design-forks.md`. When a decision is a genuine fork, **present the options with tradeoffs and let the user choose** — don't default.

Typical decisions, in rough order:
- **Navigation** — how the user moves between/within screens (`references/navigation.md`, Forks 1-2)
- **Primary action** — what it is and where it sits (`references/reach-and-ergonomics.md`, Fork 4)
- **Layout of the main content** — list / grid / carousel; density (Forks 5, 7)
- **Forms** — if there's data entry (`references/forms-and-input.md`, Fork 6)
- **Interaction model** — gestures vs. visible controls; what moves and why (`references/touch-gesture-motion.md`, Fork 3)
- **Destructive/rare actions** — where they hide (Fork 8)

Pull the relevant lens for each decision (weighting table in `designer-lenses.md`) and attribute the reasoning by name: "Rausch's taxonomy says…", "Hoober would push back here because…", "Allen and de With would split on this — …". For the Modern Craft lens, name the sub-voice (Allen / de With / Nelson), not just "Modern Craft."

**Wait gate:** present **no more than two forks at once**. Wait for the user's choice on each before presenting the next. Do not advance to Step 4 until every major pattern decision has been made.

---

## Step 4: Pressure-Test with the Lenses

Before writing the brief, run the design through the lenses that weren't already leading — especially the **Empirical Skeptic**. Load `references/touch-gesture-motion.md` for this step regardless of the feature type — the Modern Craft check needs its motion quality bars (timing, interruptibility, reduced-motion).

- **Ergonomic Realist** — does it work in the constrained grips, not just comfortable one-handed?
- **Empirical Skeptic** — is any choice justified by trend rather than evidence? Any overlay stacking?
- **Modern Craft** — what *moves*, and what does each transition communicate (Allen)? Does it feel genuinely native (de With)? Is the personality level right for this product type — and does it match the Allen-vs-de-With call if that fork came up?
- **Platform Canon** — any convention conflict? Does anything depend on in-transition iOS 26 / M3 chrome?

Check the result against the **Gotchas** list in SKILL.md. Note anything that needs a second look.

---

## Step 5: Write the Mobile Design Brief

Produce the deliverable — a brief an implementer (or another skill) can build from. Keep it tight.

```
MOBILE DESIGN BRIEF — [feature]

Primary purpose: [the one thing]
Platform(s): [web / iOS / Android]
User context: [who, and in what situation]

Content priority:
  1. [primary]   2. [secondary]   3. [...]
  Deferred: [...]   Cut: [...]

Structure decisions (with the fork chosen and why):
- Navigation: [choice] — [reasoning + lens]
- Primary action: [what] at [where] — [reasoning]
- Main content layout: [choice] — [reasoning]
- Forms: [approach, if any]
- Interaction model: [gestures + their affordances/fallbacks]
- Destructive/rare actions: [where + guard]

Ergonomics: [reach notes — what sits in which zone]
Motion: [key transitions and what each communicates]
States: [empty / loading / error — the plan for each]

Open questions / risks: [anything unresolved]
Platform-in-transition flags: [iOS 26 / M3 dependencies, if any]
```

Per the user's preference, a self-contained HTML artifact is a good format for a brief like this if the user wants something richer than markdown — offer it; otherwise markdown is fine.

**Hand-off:** name the implementation skill the brief should go to — `responsive-craft` (responsive web), `thumb-first-platform` (mobile-web/PWA technical), or the React Native/Expo skills (native). thumb-first-design decided the design; it does not write the implementation.

---

## Success Criteria

- [ ] Feature, platform, and user context understood via discovery
- [ ] Content prioritized — something cut or deferred, confirmed by the user
- [ ] Pattern decisions made as explicit forks, user chose each
- [ ] Design pressure-tested against the non-leading lenses and the gotchas
- [ ] Mobile design brief produced, with reasoning attributed to lenses
- [ ] Hand-off to the right implementation skill named
