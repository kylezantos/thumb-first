# Output Format

How the **Audit** workflow presents its findings. Two modes:

- **HTML mode (default)** — a self-contained `.html` report written to the audited project and opened in the browser. This is the richer artifact: a designer reads it, weighs the forks, and decides what to fix.
- **Terminal mode (flag-triggered)** — the plain-text report rendered inline. Use when the user passes `--terminal`, `--inline`, "show it inline," "skip the HTML," or any natural-language equivalent. No file is written.

Both modes carry the same audit content — only the rendering differs. Never summarize to fit a mode; the report is the deliverable.

> Plan mode does not emit a report. It produces the design brief defined in `workflows/plan.md`.

---

## HTML mode (default)

### Use the template — don't reinvent it

`references/report-template.html` is the canonical, worked example (a fictional cooking-assistant app, "Skillet"). **Read it and adapt it** — replace the content, keep the structure and CSS. Do not improvise a fresh layout each run; that is exactly the inconsistency this template exists to kill. The file is self-contained: one document, all CSS inlined, fonts from a CDN with a system fallback stack (degrades gracefully offline).

### Where to write it

Write to the audited project, not to a temp dir:

```
{project-root}/mobile-audits/{project-name}-mobile-audit.html
```

If the project already has a `.context/`, `docs/`, or `audits/` convention, follow it. Create the directory if needed. After writing, **open it in the default browser** (`open <path>` on macOS). Tell the user the path.

### Color discipline — the one rule that matters

The template keeps four color channels **separate**. Honor this; collapsing them is the most common way these reports turn muddy (the pre-template reports overloaded one red for "critical," "high reach cost," AND the brand color).

| Channel | Tokens | Adaptive? | Used for |
|---|---|---|---|
| **Identity** | `--ink`, `--surface*`, `--paper*`, `--muted`, `--line*` | No — fixed | All structure, text, surfaces. Warm graphite + chalk. Carries no meaning. |
| **Accent** | `--accent`, `--accent-soft`, `--accent-tint` | **Yes — sample per audit** | Brand chrome ONLY: eyebrow tick, section numerals, links, the recommended-fork ring, next-step numbers. **Never** severity, reach, or verdicts. |
| **Severity** | `--crit`, `--imp`, `--pol` (+ `-tint`) | No — fixed | Finding severity. Red / amber / cool-slate. |
| **Reach** | `--reach-low/med/high` | No — fixed | The thumb-zone diagram and its legend only. Green → amber → red. |
| **Verdict** | `--strong/concern/problem` (alias reach/severity) | No — fixed | Lens-summary verdict dots. |

### Sampling the adaptive accent

Pick **one** color from the audited product (a primary brand color from its CSS, logo, or screenshots) and set the three `--accent*` tokens (the template's Skillet example uses a terracotta). This is the single bespoke flourish; everything else stays fixed.

Guards:
- **No product to sample** (description-only audit, no visuals) → leave the template default; the report is elegant near-monochrome with only semantic color. Don't invent a brand color.
- **Sampled color collides with severity red** (a red/orange brand) → that's acceptable, because the accent only appears as brand chrome in different regions than severity badges. If it reads confusingly in practice, desaturate the accent (lower chroma) or sample the product's secondary color instead. Severity tokens never move.
- Keep accent chroma moderate. A neon accent on dark is the AI-slop tell the identity is built to avoid.

### The thumb-zone diagram — keep the pattern

The diagram is the signature element and the thing most likely to break if rebuilt freehand. The template's pattern is collision-proof — **do not** put long text labels inside the SVG:

- **Inside the phone:** numbered dots only (`1`, `2`, …), each colored by the reach zone it sits in (`--reach-low/med/high`). A number tolerates proximity; a text label does not.
- **Beside the phone:** the `.key-list` maps each number → action name → zone tag. This carries all the text.
- Plot dots by their real on-screen position (top = Ow, bottom = Natural). The radial `#reach` gradient pivots from bottom-center — the real ergonomic model of a thumb arc.
- Below the key: the three zone definitions, then a "What's off" note calling out the misplacements.

Render this section only if the audit assessed action placement across a screen. A single-component audit may not warrant it.

### Modular sections — render only what the audit produced

The visual system is fixed; the sections are not. Delete any `<section>` the audit didn't generate, and renumber the `.sec-index` labels so they stay sequential. Typical mapping from `workflows/audit.md` Step 5:

| Report section | Source | Omit when |
|---|---|---|
| Hero + stat row | scope, primary purpose, severity counts | never |
| 01 Lens summary table | Step 3 (all 8 lenses) | never — always touch all eight |
| 02 Thumb-zone diagram | Step 3 reach/placement assessment | no action-placement dimension |
| 03 Per-screen read | multi-screen audits | single-screen / single-component scope |
| 04 Findings | Step 5, severity-tiered | never (it's the point) |
| 05 The big fork | Step 4, the entangled fork | no single dominant fork |
| 06 Other forks | Step 4, remaining silent forks | none surfaced |
| 07 Limits | Step 5 "what I couldn't assess" | never — honesty about limits is required |
| 08 Next steps | Step 6 hand-offs | never |

### Findings — severity tiers, no stripes

- Group findings under `.tier-label` dividers (Critical / Important / Polish) in impact order. Within a tier, order by impact.
- Each `.finding` carries `data-sev="crit|imp|pol"` — that drives the numeral color, and Critical gets a slightly stronger border and larger title. This is the rhythm that keeps eight findings from reading as one gray wall.
- Attribute every finding to its lens(es) via `.lens-chip`. Named reasoning is what makes the audit teachable.
- Each finding body is **What → Why it matters → Fix**. The Fix is a tinted `.fix` block, not a side-stripe.

### Banned patterns (these reproduce the AI-slop the report is judged against)

- **No `border-left`/`border-right` accent stripes** > 1px on cards, findings, callouts, or fix blocks. The template uses full borders, tinted blocks, and leading numerals instead. (This was the prior reports' biggest tell.)
- **No gradient text** (`background-clip: text` + gradient).
- **No pulsing / breathing / glowing** elements — no looping scale/opacity pulses, no "live" indicators. The report has no entrance or scroll animation at all; the only motion is a quiet border-color transition on hover.
- **No banned fonts** — the template uses Bricolage Grotesque (display) / Hanken Grotesk (body) / Spline Sans Mono (labels). Keep them. Do not swap in Inter, Space Grotesk, IBM Plex, DM Sans, Instrument, etc.
- OKLCH colors, tinted neutrals, no pure black/white. The template already obeys this — keep it when editing.

### Empty / edge states

- **Zero Critical + zero Important** → the hero stat row still renders (showing `0`); replace the findings tiers with a single dimmed-italic line ("No critical or important issues — see polish and forks below."). Don't render empty tier dividers.
- **Single-screen scope** → omit sections 02, 03, and likely 05/06; the report is hero → lens table → findings → limits → next steps. Still complete, just shorter.

---

## Terminal mode (flag-triggered fallback)

When the user opts out of HTML, render the plain-text report inline. This is the format defined in `workflows/audit.md` Step 5 — the lens-summary table, prioritized findings (What / Lens / Why / Fix), silent forks, and what couldn't be assessed. No file written. Same content, same ordering, same lens attribution — just no diagram and no styling.
