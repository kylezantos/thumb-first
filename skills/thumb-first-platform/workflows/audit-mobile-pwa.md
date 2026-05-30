# Workflow: Audit Mobile App/PWA

<required_reading>
**Read these reference files before auditing:**
1. references/ios-safari-quirks.md
2. references/viewport-layout.md
3. references/pwa-manifest.md
4. references/performance.md
</required_reading>

<process>
## Step 1: Run Automated Audits

Run Lighthouse in Chrome DevTools:
```
1. Open Chrome DevTools (Cmd+Opt+I / Ctrl+Shift+I)
2. Go to Lighthouse tab
3. Select: Mobile, Performance, Accessibility, Best Practices, SEO, PWA
4. Click "Analyze page load"
```

Check specific scores:
- Performance: Should be 90+
- PWA: Should pass all checks
- Accessibility: Should be 90+

## Step 2: Check Viewport Configuration

Verify viewport meta tag in `<head>`:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```

**Red flags:**
- Missing `viewport-fit=cover` (safe areas won't work)
- `user-scalable=no` (accessibility violation)
- `maximum-scale=1` (accessibility violation on iOS)

## Step 3: Audit CSS for Mobile Issues

Search for problematic patterns:

```bash
# Find 100vh usage (potential mobile overflow)
grep -r "100vh" --include="*.css" --include="*.tsx" --include="*.jsx"

# Find fixed positioning (may conflict with safe areas)
grep -r "position:\s*fixed" --include="*.css" --include="*.tsx"

# Find small font sizes
grep -rE "font-size:\s*(0\.[0-9]+rem|[0-9]px|1[0-1]px)" --include="*.css"
```

**Check for:**
- [ ] Uses `svh`/`dvh` instead of `vh` for full-height elements
- [ ] Safe area insets applied to fixed headers/footers
- [ ] Touch targets are 44px minimum
- [ ] No horizontal overflow (test by scrolling sideways)

## Step 4: Audit PWA Configuration

Check manifest.json:
```bash
# Find manifest
find . -name "manifest*.json" -o -name "manifest.ts"
```

**Required manifest properties:**
- [ ] `name` and `short_name` (under 12 chars)
- [ ] `start_url` set to app entry point
- [ ] `display: "standalone"` for app-like experience
- [ ] `theme_color` matches app branding
- [ ] `background_color` matches splash screen
- [ ] Icons: 192x192 and 512x512 minimum
- [ ] Maskable icon with `purpose: "maskable"`

## Step 5: Audit Service Worker

Check service worker registration:
```bash
# Find service worker
find . -name "sw.js" -o -name "service-worker.js" -o -name "*sw*.ts"
```

**Verify:**
- [ ] Service worker registers successfully
- [ ] Caching strategy appropriate for content type
- [ ] Offline fallback page exists
- [ ] Cache versioning for updates

## Step 6: Test on Real iOS Device

**Critical iOS-specific checks:**
- [ ] Safe areas respected (check in landscape)
- [ ] No 100vh overflow issues
- [ ] Touch targets large enough
- [ ] No 300ms tap delay
- [ ] Rubber band scroll behavior acceptable
- [ ] PWA installs from Safari
- [ ] Status bar style correct
- [ ] Splash screen displays

**To test:**
1. Connect iPhone to Mac
2. Open Safari on iPhone, navigate to your app
3. Open Safari on Mac → Develop → [Your iPhone] → [Your page]
4. Use Web Inspector to debug

## Step 7: Test on Android

**Android-specific checks:**
- [ ] Install prompt appears (if criteria met)
- [ ] Theme color applies to address bar
- [ ] Splash screen renders correctly
- [ ] Push notifications work (if implemented)

## Step 8: Generate Audit Report

Create report with findings:

```markdown
## Mobile/PWA Audit Results

### Scores
- Lighthouse Performance: X/100
- Lighthouse PWA: X/100
- Lighthouse Accessibility: X/100

### Critical Issues
1. [Issue]: [Description]
   - File: [path]
   - Fix: [recommended action]

### Warnings
1. [Warning]: [Description]

### Passed Checks
- [ ] Viewport configured correctly
- [ ] Safe areas handled
- [ ] PWA installable
- [ ] Service worker active
- [ ] Touch targets adequate
```
</process>

<anti_patterns>
**Avoid:**
- Testing only in Chrome DevTools emulator
- Ignoring safe area insets
- Using `100vh` for mobile layouts
- Small touch targets (< 44px)
- Missing maskable icons
- Hardcoding colors that should come from manifest
</anti_patterns>

<success_criteria>
Audit is complete when:
- [ ] Lighthouse audit run and scores recorded
- [ ] Viewport and CSS issues identified
- [ ] PWA configuration verified
- [ ] Service worker checked
- [ ] Tested on real iOS device
- [ ] Tested on Android device
- [ ] Report generated with prioritized fixes
</success_criteria>
