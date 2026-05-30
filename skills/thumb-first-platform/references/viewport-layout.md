<overview>
Viewport and layout issues are the most common source of mobile web bugs. This reference covers viewport units (vh, svh, dvh, lvh), safe area insets for notches and home indicators, and proper viewport meta tag configuration.
</overview>

<viewport_meta>
## Viewport Meta Tag

<configuration name="Standard">
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
- `width=device-width`: Match device width
- `initial-scale=1.0`: No initial zoom
</configuration>

<configuration name="With Safe Areas (Required for notch support)">
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```
- `viewport-fit=cover`: Content extends into safe areas (under notch, home indicator)
- **Required** for `env(safe-area-inset-*)` to work
</configuration>

<anti_pattern name="Disabling Zoom">
```html
<!-- DON'T DO THIS - Accessibility violation -->
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, maximum-scale=1.0">
```
**Why it's bad:** Prevents users with vision impairments from zooming. iOS Safari ignores this anyway.
</anti_pattern>
</viewport_meta>

<viewport_units>
## Viewport Units

<unit name="vh (Legacy)">
**Problem:** On mobile, `100vh` equals the viewport height including browser chrome (address bar, bottom toolbar). This causes content to overflow when browser UI is visible.

```css
/* BAD - will overflow on mobile Safari/Chrome */
.hero {
  height: 100vh;
}
```
</unit>

<unit name="svh (Small Viewport Height) - RECOMMENDED">
Represents the viewport height when ALL browser UI is visible (address bar expanded).

```css
/* GOOD - consistent height, no overflow */
.hero {
  height: 100vh; /* fallback for old browsers */
  height: 100svh;
}
```

**Use for:** Hero sections, landing pages, any "full screen" static content.

**Support:** iOS 15+, Chrome 108+, Firefox 101+
</unit>

<unit name="lvh (Large Viewport Height)">
Represents the viewport height when browser UI is minimized (address bar collapsed).

```css
.expanded-view {
  min-height: 100lvh;
}
```

**Use for:** Rarely. Only when you want maximum possible height.
</unit>

<unit name="dvh (Dynamic Viewport Height)">
Changes dynamically as browser UI appears/disappears.

```css
/* Use sparingly - causes layout shifts */
.mobile-menu {
  height: 100dvh;
}
```

**Use for:** Full-screen modals, mobile menus that open at any scroll position.

**Warning:** Using `dvh` everywhere causes jarring layout shifts. Use for ~10% of cases.

**Recommendation:** Use `svh` for 90% of cases, `dvh` only for modals/menus.
</unit>

<decision_tree>
## Which Viewport Unit to Use?

```
Is this element always visible when page loads?
├── YES: Use svh
│   - Hero sections
│   - Landing pages
│   - Main content areas
│
└── NO: Does it need to fill screen at any scroll position?
    ├── YES: Use dvh
    │   - Mobile navigation menus
    │   - Full-screen modals
    │   - Overlay dialogs
    │
    └── NO: Probably don't need vh units at all
        - Use regular height/min-height
        - Let content determine height
```
</decision_tree>

<fallback_pattern>
## Fallback Pattern

For browsers that don't support new units:

```css
.full-height {
  /* Fallback for old browsers */
  height: 100vh;
  /* Modern browsers will use this */
  height: 100svh;
}

/* Or with @supports */
.full-height {
  height: 100vh;
}

@supports (height: 100svh) {
  .full-height {
    height: 100svh;
  }
}
```
</fallback_pattern>
</viewport_units>

<safe_areas>
## Safe Area Insets

Safe areas account for:
- **Top:** Notch, Dynamic Island, status bar
- **Bottom:** Home indicator (iPhone X+), gesture area
- **Left/Right:** Curved screen edges, notch in landscape

<requirement>
**Prerequisite:** Must have `viewport-fit=cover` in viewport meta tag:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```
Without this, `env(safe-area-inset-*)` values are always 0.
</requirement>

<usage name="Basic">
```css
.header {
  padding-top: env(safe-area-inset-top);
}

.bottom-nav {
  padding-bottom: env(safe-area-inset-bottom);
}
```
</usage>

<usage name="With Minimum Padding">
```css
/* Ensure minimum 1rem padding, but more if safe area requires */
.header {
  padding-top: max(1rem, env(safe-area-inset-top));
  padding-left: max(1rem, env(safe-area-inset-left));
  padding-right: max(1rem, env(safe-area-inset-right));
}

.bottom-nav {
  padding-bottom: max(1rem, env(safe-area-inset-bottom));
}
```
</usage>

<usage name="Fixed Position Elements">
```css
/* Fixed header */
.fixed-header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  padding-top: env(safe-area-inset-top);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

/* Fixed bottom bar */
.fixed-bottom {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}
```
</usage>

<usage name="CSS Custom Properties (Recommended)">
```css
:root {
  --safe-top: env(safe-area-inset-top);
  --safe-bottom: env(safe-area-inset-bottom);
  --safe-left: env(safe-area-inset-left);
  --safe-right: env(safe-area-inset-right);
}

.header {
  padding-top: max(1rem, var(--safe-top));
}
```
</usage>

<usage name="Tailwind CSS">
Add to your CSS or Tailwind config:

```css
/* globals.css */
@layer utilities {
  .pt-safe {
    padding-top: env(safe-area-inset-top);
  }
  .pb-safe {
    padding-bottom: env(safe-area-inset-bottom);
  }
  .pl-safe {
    padding-left: env(safe-area-inset-left);
  }
  .pr-safe {
    padding-right: env(safe-area-inset-right);
  }
  .px-safe {
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
  }
  .py-safe {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
  }
}
```

Usage:
```html
<header class="pt-safe px-safe">...</header>
<nav class="pb-safe px-safe">...</nav>
```
</usage>
</safe_areas>

<iphone_models>
## iPhone Safe Area Values (Reference)

| Device | Top Inset | Bottom Inset |
|--------|-----------|--------------|
| iPhone SE | 20px | 0px |
| iPhone 8 | 20px | 0px |
| iPhone X/XS | 44px | 34px |
| iPhone 11/12/13 | 47px | 34px |
| iPhone 14 Pro (Dynamic Island) | 59px | 34px |
| iPhone 14 Pro Max | 59px | 34px |
| iPhone 15/16 | Similar to 14 Pro | 34px |

**Note:** These are approximate. Always use `env()` rather than hardcoded values.
</iphone_models>

<landscape>
## Landscape Orientation

In landscape, safe areas change significantly:
- **Left/Right** insets become important (notch on side)
- **Top** inset reduces
- **Bottom** inset may change

```css
/* Handle landscape notch */
.container {
  padding-left: max(1rem, env(safe-area-inset-left));
  padding-right: max(1rem, env(safe-area-inset-right));
}

/* Orientation-specific styles */
@media (orientation: landscape) {
  .header {
    padding-top: env(safe-area-inset-top);
    padding-left: max(2rem, env(safe-area-inset-left));
    padding-right: max(2rem, env(safe-area-inset-right));
  }
}
```
</landscape>

<common_mistakes>
## Common Mistakes

<mistake name="Forgetting viewport-fit=cover">
Safe area insets return 0 without it.
</mistake>

<mistake name="Using px instead of env()">
Don't hardcode safe area values - they vary by device.
</mistake>

<mistake name="Only handling portrait">
Test in landscape - notch moves to side.
</mistake>

<mistake name="Ignoring bottom safe area">
Home indicator area is significant on iPhone X+.
</mistake>

<mistake name="Using 100vh for app shell">
Use 100svh or calc with safe areas.
</mistake>
</common_mistakes>
