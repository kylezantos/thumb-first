---
name: thumb-first-platform
description: "The technical-verification layer of the thumb-first mobile suite — finds and fixes objective platform defects in mobile web apps and PWAs (iOS Safari, Android Chrome). Covers viewport/100vh bugs, safe-area insets, touch targets and tap delay, service workers, web push, PWA manifest, and Core Web Vitals — with file/line defects and fixes, not opinions. Target-aware: full coverage for web/PWA; for native apps (React Native, Expo, SwiftUI, Flutter) it detects the target and hands off to a native skill if one is installed, otherwise states plainly that it does not do native-platform audits. Use for mobile-web/PWA technical audits, safe-area/notch issues, 100vh overflow, service-worker/offline, install/manifest, or mobile performance. Usually run via the /thumb-first umbrella (which also runs the design pass); invoke directly for technical checks only."
argument-hint: "[audit|ios|pwa|perf|test]"
---

# Thumb-First · Platform

The **technical-verification layer** of the thumb-first suite. Where `thumb-first-design` decides *what* the mobile design should be (platform-agnostic judgment), this skill verifies that the *implementation* holds up on the actual device — objective defects with file:line and fixes, rated by severity.

Run `/thumb-first` for a combined design + platform review; use this skill directly for technical checks only.

---

## Step 0 · Detect the Target (do this first)

The relevant defects are completely different for a web/PWA vs. a native app. Determine the target before doing anything else.

**Sniff the repo:**
- **Web / PWA** → `next.config.*`, `vite.config.*`, an `index.html`, a web app `manifest.json`/`manifest.webmanifest`, a service worker, Tailwind/CSS. **This is this skill's home turf — proceed to the principles and routing below.**
- **Native** → `react-native`/`expo` in `package.json`, `app.json`/`app.config.*` (Expo), an `ios/`+`android/` pair, `*.xcodeproj`/`Package.swift`/`*.swift` (SwiftUI/UIKit), or `pubspec.yaml` (Flutter).
- **Ambiguous or both** (e.g. an Expo app with a web target) → ask which target to audit.

**If the target is NATIVE — do not fake a web audit.** This skill's checks (Safari quirks, viewport units, service workers, web manifest) do not apply. Instead:

1. **Check for an installed native skill** to hand off to — scan available skills / `~/.claude/skills/` for ones describing React Native, Expo, SwiftUI, native iOS/Android, or Flutter (e.g. `react-native-design`, `building-native-ui`, `vercel-react-native-skills`).
2. **If one exists → hand off.** Name it explicitly: "For the native technical pass, use `<skill>` — it covers native performance, lists, and platform APIs." Note that `thumb-first-design` still covers the design judgment for native, since that layer is platform-agnostic.
3. **If none exists → say so plainly. Do not overreach:**
   > thumb-first-platform covers mobile **design judgment** (via `thumb-first-design`) and **web/PWA** technical defects. It is **not** set up to run native-platform (React Native / SwiftUI / Flutter) performance or technical audits. For that, install a native-specific skill, or use platform-native tooling — Xcode Instruments, Android Studio Profiler, Flipper, or the React Native performance monitor.

The design layer applies to every target. This technical layer is web/PWA-deep and native-aware-but-delegating. Be honest about that boundary.

---

## Mobile Web & PWA — Core Principles

### 1. iOS Safari is the problem child
Most quirks and limitations live here. Always test on real iOS devices — simulators miss critical behaviors. Key pain points:
- PWAs require Safari for installation (Add to Home Screen)
- Push notifications only work when installed to home screen (iOS 16.4+)
- 7-day storage cap for non-installed web apps; 50MB cache limit; no background sync
- WebKit-only rendering (even Chrome on iOS uses WebKit)

### 2. Viewport units matter
Never use `100vh` for full-height layouts on mobile — browser chrome causes overflow.
```css
.hero  { height: 100vh; height: 100svh; }  /* svh for static elements (90% of cases) */
.menu  { height: 100dvh; }                 /* dvh only for elements that should resize */
```
`dvh` causes layout shifts — use sparingly.

### 3. Safe-area insets are required
Notches, Dynamic Islands, and home indicators are everywhere.
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```
```css
.header     { padding-top: max(1rem, env(safe-area-inset-top)); }
.bottom-nav { padding-bottom: max(1rem, env(safe-area-inset-bottom)); }
```

### 4. Touch targets ≥ 44px
Apple HIG requires 44×44pt minimum; Google recommends 48×48dp. Never go smaller for tap targets.

### 5. Test on real devices
Chrome DevTools emulation lies — it doesn't simulate address-bar behavior, safe-area insets, touch-delay nuances, iOS Safari CSS bugs, or real mobile-hardware performance. Use real devices or BrowserStack/Sauce Labs.

---

## Intake & Routing

**What do you want to do?**

1. Audit an existing mobile app/PWA for issues
2. Fix iOS Safari–specific problems
3. Set up PWA (manifest, service worker, icons)
4. Optimize mobile performance (Core Web Vitals)
5. Debug touch/gesture issues
6. Set up push notifications
7. Test mobile compatibility

Wait for the response, then read the matching workflow and follow it.

| Response | Workflow / Reference |
|----------|----------------------|
| 1 · "audit", "check", "review", "issues" | `workflows/audit-mobile-pwa.md` |
| 2 · "ios", "safari", "iphone", "apple" | `workflows/fix-ios-issues.md` |
| 3 · "pwa", "manifest", "service worker", "install" | `workflows/setup-pwa.md` |
| 4 · "performance", "slow", "vitals", "lcp", "cls" | `workflows/optimize-performance.md` |
| 5 · "touch", "gesture", "tap", "swipe", "scroll" | read `references/touch-interactions.md`, then fix |
| 6 · "push", "notification", "vapid" | read `references/push-notifications.md`, then implement |
| 7 · "test", "debug", "emulate" | `workflows/test-mobile.md` |

**Intent-based routing (specific problem described):**
- "100vh not working", "viewport", "height issue", "notch", "safe area", "dynamic island" → `references/viewport-layout.md`
- "images too big", "slow images" → `references/image-optimization.md`
- "rubber band", "bounce scroll" → `references/ios-safari-quirks.md`

> **When run via `/thumb-first`:** the umbrella invokes the **audit** path only (`workflows/audit-mobile-pwa.md`) and merges its defects into the combined report. The setup/push/perf workflows are build tasks — available on direct invocation, but outside the one-stop review.

---

## Verification Loop — After Every Change

1. **Build succeeds?** `pnpm build`
2. **Lighthouse audit?** Chrome DevTools → Lighthouse → Mobile + PWA
3. **Real iOS device?** Safari Web Inspector, or ngrok/localtunnel
4. **Safe areas handled?** Portrait and landscape, with and without notch

Report findings as: `Build: ✓` · `Lighthouse PWA: X/100` · `iOS Safari: [specific issues]`.

---

## Reference Index

All in `references/`:

| File | Contents |
|------|----------|
| ios-safari-quirks.md | iOS Safari bugs, CSS workarounds, limitations |
| android-pwa.md | Android Chrome PWA behavior, WebAPK |
| viewport-layout.md | 100vh issues, dvh/svh/lvh, safe-area insets |
| pwa-manifest.md | manifest.json, icons, splash screens |
| service-workers.md | Caching strategies, offline support, Next.js integration |
| push-notifications.md | Web push, VAPID, iOS vs Android |
| touch-interactions.md | 300ms delay, touch-action, gestures |
| performance.md | Core Web Vitals (LCP/INP/CLS), mobile optimization |
| image-optimization.md | Next.js Image, responsive images, WebP/AVIF |
| responsive-patterns.md | Common mistakes, anti-patterns |
| testing-debugging.md | Lighthouse, testing tools, iOS debugging |

## Workflow Index

| File | Purpose |
|------|---------|
| audit-mobile-pwa.md | Comprehensive audit of a mobile app/PWA (the path `/thumb-first` calls) |
| fix-ios-issues.md | Fix iOS Safari–specific problems |
| setup-pwa.md | Configure a PWA from scratch |
| optimize-performance.md | Improve Core Web Vitals |
| test-mobile.md | Testing procedures |

---

## Common Issues & Quick Fixes

```css
/* 100vh overflow on mobile */     height: 100svh;            /* or min-height */
/* 300ms tap delay */              button, a { touch-action: manipulation; }
/* rubber-band scroll */           html { overscroll-behavior: none; }
```
```html
<!-- safe area not working -->            <meta name="viewport" content="..., viewport-fit=cover">
<!-- iOS status bar style -->             <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```
**PWA not installable:** valid `manifest.json` · HTTPS · service worker registered · icons include 192×192 and 512×512.
