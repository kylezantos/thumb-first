<overview>
iOS Safari has the most quirks and limitations of any modern browser for mobile web development. This is because Apple restricts all browsers on iOS to use WebKit (even Chrome on iOS uses WebKit), and Safari's PWA support lags behind Chrome on Android. Understanding these quirks is essential for building mobile web apps that work well on iPhones and iPads.
</overview>

<platform_versions>
## iOS Version Support (2024-2025)

| iOS Version | Safari Version | Key Features/Changes |
|-------------|----------------|---------------------|
| iOS 18 | Safari 18 | Latest, improved PWA support |
| iOS 17.4+ | Safari 17.4 | EU browser choice, other browsers can install PWAs |
| iOS 16.4+ | Safari 16.4 | **Web Push notifications**, Add to Home Screen from other browsers |
| iOS 16+ | Safari 16 | `overscroll-behavior` support |
| iOS 15 | Safari 15 | dvh/svh/lvh viewport units |
| iOS 14 | Safari 14 | Legacy, still in use |

**Target minimum:** iOS 15 for modern features, iOS 14 for maximum compatibility.
</platform_versions>

<pwa_limitations>
## PWA Limitations on iOS

<limitation name="Installation">
**No automatic install prompt.** Users must manually "Add to Home Screen" via Safari's Share menu. Other browsers gained this ability in iOS 16.4+.

**Workaround:** Add visible instructions or an install banner explaining how to add to home screen.
</limitation>

<limitation name="Storage">
**50MB cache limit** for PWAs. IndexedDB and Cache API combined.

**7-day storage cap** for web apps NOT installed to home screen. If user doesn't visit within 7 days, all data may be cleared.

**No shared storage** between Safari and standalone PWA. Data stored in Safari won't be accessible when app is launched from home screen.

**Workaround:** Encourage users to install the PWA. Use server-side storage for important data.
</limitation>

<limitation name="Push Notifications">
Only works when:
- PWA is installed to home screen
- User grants permission
- iOS 16.4 or later

**No background sync** - notifications only arrive when app is in foreground or receives push.

**Workaround:** Be explicit that push notifications require home screen installation.
</limitation>

<limitation name="Background Processing">
No background sync, no periodic background fetch. PWA cannot run code when not in foreground.

**Workaround:** Sync data on app open, use server-side processing.
</limitation>

<limitation name="Device APIs">
Limited access to:
- No Bluetooth Web API
- No USB Web API
- No NFC
- Limited geolocation (works but with prompts)
- No widgets
- No direct printing

**Workaround:** For these features, consider native app or use fallback experiences.
</limitation>
</pwa_limitations>

<css_bugs>
## CSS Bugs and Workarounds

<bug name="100vh Overflow">
**Problem:** `100vh` includes the Safari address bar, causing content to overflow.

**Solution:**
```css
/* Use svh for static full-height elements */
.hero {
  height: 100vh; /* fallback */
  height: 100svh;
}

/* Use dvh only for dynamic elements like modals */
.modal {
  height: 100dvh;
}
```
</bug>

<bug name="iOS 26 100dvh Gap">
**Problem:** After iOS 26, overlays using `100dvh` show a gap at the bottom.

**Solution:**
```css
/* Use 100vh for overlays on iOS 26+ */
.overlay {
  height: 100vh;
  /* Or use svh with fallback */
  height: 100svh;
}
```
</bug>

<bug name="Fixed Position with Keyboard">
**Problem:** When virtual keyboard opens, fixed elements may jump or be positioned incorrectly.

**Solution:**
```css
/* Use visualViewport API */
.fixed-bottom {
  position: fixed;
  bottom: 0;
}
```

```javascript
// JavaScript adjustment
if (window.visualViewport) {
  window.visualViewport.addEventListener('resize', () => {
    document.documentElement.style.setProperty(
      '--keyboard-inset',
      `${window.innerHeight - window.visualViewport.height}px`
    );
  });
}
```
</bug>

<bug name="Flexbox Inconsistencies">
**Problem:** Safari interprets flex shorthand differently.

**Solution:**
```css
/* Be explicit, avoid shorthand */
.flex-item {
  flex-grow: 0;
  flex-shrink: 0;
  flex-basis: auto;
  min-width: 0; /* Prevent overflow */
}
```
</bug>

<bug name="Details/Summary Emoji (iOS 26.1)">
**Problem:** Details disclosure triangle renders as emoji ▶️ instead of ▸.

**Solution:**
```css
details > summary {
  list-style-type: "▸ ";
}
details[open] > summary {
  list-style-type: "▾ ";
}
```
</bug>

<bug name="CSS Zoom Property">
**Problem:** `zoom` CSS property doesn't work correctly on iOS.

**Solution:** Use `transform: scale()` instead, but note it doesn't affect layout size.
</bug>
</css_bugs>

<scroll_behavior>
## Scroll Behavior

<issue name="Rubber Band Bounce">
**Problem:** Elastic overscroll at page boundaries.

**Solution (Safari 16+):**
```css
html {
  overscroll-behavior: none;
}
```

**Solution (older iOS):** Use a wrapper div:
```css
.page-wrapper {
  height: 100svh;
  overflow-y: auto;
  overscroll-behavior: contain;
  -webkit-overflow-scrolling: touch;
}
```
</issue>

<issue name="Scroll Lock for Modals">
**Problem:** Background scrolls when modal is open.

**Solution:**
```javascript
// Opening modal
const scrollY = window.scrollY;
document.body.style.position = 'fixed';
document.body.style.top = `-${scrollY}px`;
document.body.style.width = '100%';

// Closing modal
const scrollY = document.body.style.top;
document.body.style.position = '';
document.body.style.top = '';
document.body.style.width = '';
window.scrollTo(0, parseInt(scrollY || '0') * -1);
```
</issue>

<issue name="Momentum Scrolling">
**Solution:** Always include for smooth scrolling:
```css
.scroll-container {
  -webkit-overflow-scrolling: touch;
}
```
</issue>
</scroll_behavior>

<status_bar>
## Status Bar Styling

```html
<!-- Enable standalone mode -->
<meta name="apple-mobile-web-app-capable" content="yes">

<!-- Status bar style options -->
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<!-- default: white background, black text -->
<!-- black: black background (looks all black) -->
<!-- black-translucent: transparent, content extends under (DEPRECATED but works) -->
```

**Note:** `black-translucent` is deprecated. For content extending under status bar, use `viewport-fit=cover` and safe area insets instead.
</status_bar>

<debugging>
## Debugging iOS Safari

<method name="Safari Web Inspector">
**Requirements:**
- Mac with Safari
- iPhone/iPad with Safari
- USB cable

**Setup:**
1. iPhone: Settings → Safari → Advanced → Web Inspector → ON
2. Connect iPhone to Mac
3. Open Safari on iPhone, navigate to your page
4. Mac Safari: Develop menu → [Your iPhone] → [Your page]

**Features:**
- Full DOM inspection
- Console access
- Network monitoring
- Performance profiling
</method>

<method name="Eruda (No Mac)">
If you don't have a Mac, inject Eruda into your page:

```html
<script src="https://cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
```

Provides in-page DevTools console and element inspector.
</method>

<method name="Remote Debugging via ngrok">
```bash
npx ngrok http 3000
```
Access the provided URL on any device for testing.
</method>
</debugging>

<eu_dma_2024>
## EU Digital Markets Act (2024)

In early 2024, Apple briefly removed PWA support in the EU, then reversed this decision.

**Current status:** PWAs work in the EU. iOS 17.4+ allows other browsers (Chrome, Firefox, Edge) to use their own engines and install PWAs.

**Impact:** EU users may have better PWA support through non-Safari browsers.
</eu_dma_2024>
