# Unified Report — Merged Output Structure

The one-stop review produces a single self-contained HTML report. Its defining job: present two **fundamentally different kinds of finding** without letting them blur together. An opinion must never read as a defect; a must-fix must never read as a preference.

## Base template

Start from `../thumb-first-design/references/report-template.html` — the canonical thumb-first report. It **already ships** the merged-review sections as a worked example: a **Fix order** section right after the header and a **Platform defects** section after the findings, plus all their CSS and the documented steel platform channel (`--plat`) and P0–P3 ramp. So you are **populating** these sections, not building them — replace the worked-example content, keep the structure, classes, and CSS. A design-only audit deletes both sections; a full review fills them in. Do not reinvent the markup or restyle the channels.

The template ships a pure-CSS **Dark / Light** toggle (top-right of `.wrap`) with a full `:root:has(#theme-light:checked)` light-mode token set — the merged report inherits it for free. Keep the toggle markup and both token blocks; if you re-sample the adaptive accent, set its dark **and** light value. See `../thumb-first-design/references/output-format.md` → "Dual theme (light / dark)".

## The two finding classes (keep visually distinct)

### Class A — Design & judgment
- **Source:** the design pass (`thumb-first-design`).
- **Nature:** subjective; often a *fork* with more than one valid answer.
- **Severity vocabulary:** Critical / Important / Polish (user-impact), plus "Decision needed" for forks.
- **Styling:** the report's existing amber/neutral lens-and-fork treatment. Forks render as option cards with tradeoffs, not as bugs.
- **Label every item** with the lens that caught it (Hoober, Hurff, Clark, …).

### Class B — Platform defects
- **Source:** the platform pass (`thumb-first-platform`), web/PWA targets.
- **Nature:** objective; right-or-wrong.
- **Severity vocabulary:** P0 / P1 / P2 / P3.
- **Styling:** a distinct **red severity scale** section, clearly separated from Class A. Each defect carries **file:line** and a concrete fix.
- If the target was native: this section is replaced by a short **handoff/limitation** note (which native skill to use, or that native audits aren't covered) — never fabricated web defects.

Use a visible structural divider and different badge shapes/colors between the two classes. A reader skimming on a phone must be able to tell at a glance whether a row is "a decision I should make" or "a bug I must fix."

## The combined fix-order (the reconciliation)

After the two class sections, add one **interleaved priority list** — the single most useful artifact of running both passes together. Rank across both classes by real user impact:

```
1. [P0 · defect]   Safe-area inset missing → content under the home indicator        thumb-first-platform · file:line
2. [Critical · design] Primary action sits in the top "Ow" zone — move to lower band  thumb-first-design · Hoober
3. [P1 · defect]   100vh overflow on the game screen                                  thumb-first-platform · file:line
4. [Important · fork]  Tab bar vs. bottom sheet for navigation — decision needed      thumb-first-design · Rausch
…
```

Keep the class tag (`defect` / `design` / `fork`) and source on every line so the priority list never erases the distinction — it orders across classes without merging them.

## Sections, in order

1. **Header** — what was reviewed, target (web/PWA or native), device class, date, counts (N design findings · M forks · K platform defects).
2. **Combined fix-order** — the interleaved list above (lead with it; it's what the user acts on).
3. **Design & judgment** — Class A, full detail (lens reads, forks with options).
4. **Platform defects** — Class B, full detail (file:line, fix), or the native handoff note.
5. **What I couldn't assess** — limits of each pass (e.g. "kid-hand reach is adult-grip data"; "no real-device test run").

## Anti-goals

- Don't average the two severity scales into one number — P0 and "Critical" are different currencies; show both, don't fuse.
- Don't bury the platform defects inside the design narrative or vice versa.
- Don't let the umbrella restate design or platform *content* — link to / invoke the siblings; this report only arranges their output.
