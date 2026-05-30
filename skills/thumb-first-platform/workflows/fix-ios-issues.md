# Workflow: Fix iOS Safari Issues

<required_reading>
**Read these reference files before fixing:**
1. references/ios-safari-quirks.md
2. references/viewport-layout.md
3. references/touch-interactions.md
</required_reading>

<process>
## Step 1: Identify the iOS Issue Category

Common iOS Safari issues:

| Symptom | Likely Cause | Reference |
|---------|--------------|-----------|
| Page overflows vertically | 100vh includes browser chrome | viewport-layout.md |
| Content behind notch | Missing safe area insets | viewport-layout.md |
| Tap delay feels slow | Missing touch-action | touch-interactions.md |
| Rubber band bounce annoying | Missing overscroll-behavior | ios-safari-quirks.md |
| PWA won't install | Manifest or HTTPS issues | pwa-manifest.md |
| Storage getting cleared | 7-day cap for non-installed PWAs | ios-safari-quirks.md |
| Push notifications not working | PWA not installed to home screen | push-notifications.md |
| Fixed element jumps around | Keyboard or scroll issues | ios-safari-quirks.md |

## Step 2: Fix Viewport Height Issues

**Problem:** `100vh` includes the Safari address bar, causing overflow.

**Solution:** Use new viewport units:

```css
/* Replace 100vh with svh for static full-height elements */
.hero {
  min-height: 100vh; /* fallback for old browsers */
  min-height: 100svh;
}

/* Use dvh ONLY for elements that should resize with browser chrome */
.mobile-menu {
  height: 100dvh;
}
```

**Decision tree:**
- Static hero/landing page → `100svh`
- Full-screen modal that opens at any scroll position → `100dvh`
- Element that should fill available space → `100svh`

## Step 3: Fix Safe Area Issues

**Problem:** Content hidden behind notch, Dynamic Island, or home indicator.

**Step 3.1:** Enable viewport-fit in HTML:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```

**Step 3.2:** Apply safe area padding:

```css
/* Header - account for notch/Dynamic Island */
.header {
  padding-top: env(safe-area-inset-top);
  /* Or with minimum padding */
  padding-top: max(1rem, env(safe-area-inset-top));
}

/* Bottom navigation - account for home indicator */
.bottom-nav {
  padding-bottom: env(safe-area-inset-bottom);
}

/* Full-width elements in landscape */
.container {
  padding-left: max(1rem, env(safe-area-inset-left));
  padding-right: max(1rem, env(safe-area-inset-right));
}
```

**Tailwind CSS approach:**
```css
/* In globals.css or tailwind config */
@supports (padding: env(safe-area-inset-top)) {
  .safe-top { padding-top: env(safe-area-inset-top); }
  .safe-bottom { padding-bottom: env(safe-area-inset-bottom); }
  .safe-left { padding-left: env(safe-area-inset-left); }
  .safe-right { padding-right: env(safe-area-inset-right); }
}
```

## Step 4: Fix Tap Delay

**Problem:** 300ms delay on taps feels sluggish.

**Solution:** Apply touch-action CSS:

```css
/* Apply to all interactive elements */
a, button, input, select, textarea, label, summary, [role="button"] {
  touch-action: manipulation;
}

/* Or globally (less targeted) */
* {
  touch-action: manipulation;
}
```

## Step 5: Fix Scroll Issues

**Problem:** Rubber band bounce scrolling, scroll locking for modals.

**For rubber band bounce:**
```css
html {
  overscroll-behavior: none;
}
```

**For modal scroll lock (iOS-safe approach):**
```tsx
// When opening modal
document.body.style.position = 'fixed';
document.body.style.top = `-${window.scrollY}px`;
document.body.style.width = '100%';

// When closing modal
const scrollY = document.body.style.top;
document.body.style.position = '';
document.body.style.top = '';
document.body.style.width = '';
window.scrollTo(0, parseInt(scrollY || '0') * -1);
```

## Step 6: Fix Status Bar Styling

**For standalone PWA:**
```html
<!-- Required for standalone mode -->
<meta name="apple-mobile-web-app-capable" content="yes">

<!-- Status bar options: default, black, black-translucent -->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

**Note:** `black-translucent` is deprecated but still works. It makes content extend under status bar.

## Step 7: Fix iOS-Specific CSS Bugs

**Flexbox inconsistencies:**
```css
/* Be explicit with flex properties */
.flex-container {
  display: flex;
  flex-direction: row; /* explicit, not shorthand */
  min-width: 0; /* prevent flex item overflow */
}

.flex-item {
  flex: 0 0 auto; /* explicit basis */
  min-width: 0;
}
```

**Position: fixed with keyboard:**
```css
/* Fixed elements that should stay visible with keyboard */
.input-toolbar {
  position: fixed;
  bottom: 0;
  /* iOS keyboard pushes fixed elements, use this to adjust */
  bottom: env(keyboard-inset-height, 0);
}
```

## Step 8: Verify Fixes

Test on real iOS device:
1. Connect iPhone to Mac
2. Open Safari → Your app
3. Safari on Mac → Develop → [iPhone] → [Page]
4. Test in:
   - Portrait and landscape
   - With and without keyboard open
   - As PWA installed to home screen
   - In Safari browser (not installed)
</process>

<anti_patterns>
**Avoid:**
- Using JavaScript libraries like FastClick (obsolete since iOS 9.3)
- Disabling zoom with `user-scalable=no` (accessibility violation)
- Using `constant()` instead of `env()` for safe areas (deprecated)
- Testing only in Chrome DevTools
- Assuming iOS behavior matches Android
</anti_patterns>

<success_criteria>
iOS issues are fixed when:
- [ ] No vertical overflow from viewport height
- [ ] Safe areas respected on all iPhone models
- [ ] No perceptible tap delay
- [ ] Scroll behavior is smooth and controlled
- [ ] PWA status bar styled correctly
- [ ] All fixes verified on real iOS device
</success_criteria>
