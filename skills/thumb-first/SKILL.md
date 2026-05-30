---
name: thumb-first
description: "One-stop mobile review — the umbrella for the thumb-first suite. Runs a design-judgment pass AND a platform-technical pass over a mobile app/screen/PWA, then merges them into ONE prioritized report that keeps design forks (opinions) and platform defects (objective, P0–P3) visually distinct. Use when the user wants to review, audit, critique, or design a mobile app, screen, or PWA — 'review my mobile app', 'is this good mobile UX', 'audit this screen', 'check my PWA'. Routes to: full review (default), design-only (thumb-first-design), or platform-only (thumb-first-platform). Platform-agnostic on judgment; web/PWA-deep on technical, with native handoff."
argument-hint: "[review|design|platform]"
---

# Thumb-First · Mobile Review (umbrella)

The one-stop entry point for the thumb-first mobile suite. It coordinates two independently-maintained sibling skills and merges their output:

| Layer | Skill | Answers | Output |
|-------|-------|---------|--------|
| **Design judgment** | `thumb-first-design` | *Is this the right mobile design?* (platform-agnostic) | forks + opinions, by design impact |
| **Platform technical** | `thumb-first-platform` | *Is it built correctly for the device?* (web/PWA-deep, native-handoff) | objective defects, P0–P3, file:line |

The two layers have different shelf-lives — design judgment is durable; platform specifics rot with each OS/browser release — so they live as **separate skills** and are versioned independently. This umbrella is the seam that runs both and reconciles them. Keeping the two finding classes visually distinct in the merged report is the whole point: never let an opinion read as a defect, or a must-fix read as a preference.

---

## Mode Selection

Detect from `$ARGUMENTS`, else default to **full review**.

| Mode | Trigger | What runs |
|------|---------|-----------|
| **Full review** (default) | "review", "audit", bare invocation, or anything spanning design + tech | Both passes → merged report |
| **Design only** | "design", "judgment", "patterns", "forks", "lenses" | `thumb-first-design` only |
| **Platform only** | "platform", "technical", "PWA", "safe area", "viewport", "performance", "defects" | `thumb-first-platform` only |

For design-only or platform-only, hand off to that sibling skill and stop — don't run the merge.

If the user picked a review but gave nothing to review, ask what they're reviewing — a screenshot, a live URL, code, or a description — before routing. Both passes can work from any of these; they just need one.

---

## Full Review — Orchestration

Read [`workflows/full-review.md`](workflows/full-review.md) and follow it. The shape:

### Step 0 · Detect the target once
Sniff the repo for **web/PWA** (`next.config`, `vite`, web `manifest`, service worker) vs **native** (`react-native`/`expo`, `app.json`, `ios/`+`android/`, `*.swift`, `pubspec.yaml`); if ambiguous, ask. Pass the result to both passes — the platform pass branches hard on it (see `thumb-first-platform` Step 0; native triggers handoff or an honest limitation, never a faked web audit).

### Step 1 · Design pass
Run `thumb-first-design`'s audit (`../thumb-first-design/workflows/audit.md`, loading `../thumb-first-design/references/designer-lenses.md`). Produces design findings + the silently-decided forks. This layer applies to **every** target.

### Step 2 · Platform pass
Run `thumb-first-platform`'s audit path (`../thumb-first-platform/workflows/audit-mobile-pwa.md`) for web/PWA, or take its native branch (handoff / stated limitation). Produces objective defects with file:line and P0–P3.

> Steps 1 and 2 are independent — run them as two parallel subagents when speed matters, then merge. Sequential in the main context is fine for small surfaces.

### Step 3 · Merge
Combine into ONE report per [`references/unified-report.md`](references/unified-report.md):
- **Design & judgment** — forks and opinions, "decision needed", ranked by user impact. Distinct styling (amber/neutral).
- **Platform defects** — objective, P0–P3, file:line, "must fix". Distinct styling (red severity scale).
- **Combined fix-order** — one prioritized list interleaving both, so the reader sees what to do first regardless of class.

Base the HTML on `../thumb-first-design/references/report-template.html` (the canonical report) and add the platform-defects section — don't reinvent the template.

---

## What this umbrella is NOT

- It does **not** hold design or platform knowledge itself — that lives in the two sibling skills. This file is routing + merge only. If you're tempted to add a designer lens or a Safari quirk here, it belongs in the sibling.
- It does **not** write implementation. Design findings hand off to `responsive-craft` / RN-Expo skills; platform defects are fixed by `thumb-first-platform`'s fix workflows.

## Suite Map

```
thumb-first/            ← this umbrella (routing + merge)
thumb-first-design/     ← design judgment (8 lenses, forks) — platform-agnostic
thumb-first-platform/   ← technical verification (web/PWA deep, native handoff)
```

## Reference & Workflow Index

| File | Contents | Load when |
|------|----------|-----------|
| [workflows/full-review.md](workflows/full-review.md) | The two-pass orchestration + merge procedure | Running a full review |
| [references/unified-report.md](references/unified-report.md) | Merged report structure — two distinct finding classes + combined fix-order | Producing the report |
