# Full Review — Orchestration

Runs the design pass and the platform pass over the same mobile target, then merges them into one prioritized report. This is what `/thumb-first` does by default.

## Required Reading

- This file.
- `references/unified-report.md` — the merged output structure.
- At run time you will load, from the sibling skills:
  - `../thumb-first-design/workflows/audit.md` + `../thumb-first-design/references/designer-lenses.md`
  - `../thumb-first-platform/workflows/audit-mobile-pwa.md` (web/PWA) — or take its native branch.

## Step 0 · Establish scope and detect the target

1. Confirm what's being reviewed: a screenshot, a live URL, code/repo, or a description. If nothing was provided, ask. One is enough.
2. **Detect the target platform** (do this once; both passes need it):
   - **Web / PWA** → `next.config.*`, `vite.config.*`, `index.html`, web `manifest.json`/`manifest.webmanifest`, a service worker.
   - **Native** → `react-native`/`expo` in `package.json`, `app.json`/`app.config.*`, an `ios/`+`android/` pair, `*.xcodeproj`/`Package.swift`/`*.swift`, or `pubspec.yaml`.
   - **Ambiguous / both** → ask which target to review.
3. Note the device class (phone vs tablet, iOS vs Android) — reach math and safe-area behavior differ, and both passes should know it.

## Step 1 · Design pass (always runs)

Run the `thumb-first-design` audit against the scope. Load its `designer-lenses.md`, weight the lenses to the context, surface the decision forks, and produce findings ranked by **user impact** (critical / important / polish). This layer is platform-agnostic — it runs identically for web and native.

Capture: design findings (with the lens that caught each) and the silently-decided forks.

## Step 2 · Platform pass (target-aware)

- **Web / PWA** → run `thumb-first-platform`'s audit path. Produce objective defects: viewport/100vh, safe-area insets, touch targets, tap delay, service worker/offline, manifest/install, Core Web Vitals — each with **file:line** and a **P0–P3** severity.
- **Native** → take `thumb-first-platform`'s Step 0 native branch: if a native skill is installed (`react-native-design`, `building-native-ui`, `vercel-react-native-skills`, etc.), name it as the handoff for the native technical pass; if none, state plainly that the suite does design judgment + web/PWA technical only and does not run native-platform audits. **Do not fabricate web defects for a native target.**

> **Parallelize when it pays.** Steps 1 and 2 are independent. For a large surface, run each as its own subagent and merge their structured results. For a single screen, sequential in the main context is simpler.

## Step 3 · Merge into one report

Follow `references/unified-report.md`. Two **visually distinct** finding classes, never blended:

- **Design & judgment** — forks + opinions, "decision needed", amber/neutral styling, ranked by user impact.
- **Platform defects** — objective, P0–P3, file:line, "must fix", red severity scale.

Then produce a **combined fix-order**: one interleaved list (e.g. a P0 defect and a critical design fork both land at the top), so the reader sees what to do first regardless of class. Base the HTML on `../thumb-first-design/references/report-template.html` and add the platform-defects section — don't rebuild the template.

## Step 4 · Wait gate

Present the merged report and stop. Then offer next steps:
- Hand design fixes to `responsive-craft` / native skills.
- Hand platform defects to `thumb-first-platform`'s fix workflows (`fix-ios-issues.md`, `optimize-performance.md`).

## Success Criteria

- [ ] Target detected (web/PWA vs native) before either pass; native correctly handed off or limited, not faked.
- [ ] Both passes ran (or platform pass correctly delegated for native).
- [ ] Report keeps the two finding classes visually distinct — no opinion styled as a defect, no must-fix styled as a preference.
- [ ] A single combined fix-order interleaves both classes by priority.
- [ ] Knowledge stayed in the siblings — this workflow only routed and merged.
