<overview>
Testing mobile web apps requires a combination of automated tools, browser emulation, and real device testing. This reference covers Lighthouse audits, Chrome DevTools, Safari Web Inspector, and debugging techniques for iOS and Android.
</overview>

<lighthouse>
## Lighthouse Audits

<running>
**Chrome DevTools:**
1. Open DevTools (Cmd+Opt+I / Ctrl+Shift+I)
2. Go to Lighthouse tab
3. Select:
   - Mode: Navigation (default)
   - Device: Mobile
   - Categories: Performance, Accessibility, Best Practices, SEO, PWA
4. Click "Analyze page load"
</running>

<cli>
**Command Line:**
```bash
# Install
npm install -g lighthouse

# Run audit
lighthouse https://example.com --preset=perf --output=html --output-path=./report.html

# Mobile-specific
lighthouse https://example.com \
  --emulated-form-factor=mobile \
  --throttling.cpuSlowdownMultiplier=4 \
  --output=json
```
</cli>

<key_audits>
**Performance:**
- LCP (Largest Contentful Paint): < 2.5s
- INP (Interaction to Next Paint): < 200ms
- CLS (Cumulative Layout Shift): < 0.1

**PWA (Note: PWA audits deprecated in Lighthouse):**
- Service worker registered
- Manifest valid
- HTTPS
- Offline fallback

**Accessibility:**
- Color contrast
- Touch targets
- Alt text
- Focus management

**Best Practices:**
- HTTPS
- No console errors
- Image aspect ratios
</key_audits>

<limitations>
**Lighthouse limitations:**
- Simulates mobile, doesn't use real device
- May not catch iOS-specific bugs
- Network throttling differs from real conditions
- Scores vary between runs
</limitations>
</lighthouse>

<chrome_devtools>
## Chrome DevTools Mobile Emulation

<device_mode>
**Enable device mode:**
- Click device toolbar icon (or Cmd+Shift+M / Ctrl+Shift+M)
- Select device from dropdown

**Available presets:**
- iPhone SE, 14 Pro, 14 Pro Max
- Pixel 7, Galaxy S20
- iPad Mini, iPad Pro
- Responsive (custom dimensions)
</device_mode>

<features>
**Useful features:**
- **Network throttling:** Network tab → Throttling → Slow 3G
- **CPU throttling:** Performance tab → Capture settings → CPU: 4x slowdown
- **Touch simulation:** Automatically enabled in device mode
- **Geolocation:** Sensors tab → Location
- **Device orientation:** Sensors tab → Orientation
</features>

<limitations>
**What DevTools can't simulate:**
- iOS Safari CSS bugs
- Real touch behavior nuances
- Safe area insets (approximates)
- Actual mobile CPU performance
- iOS keyboard behavior
- WebKit-specific issues (Chrome uses Blink on desktop)

**Always supplement with real device testing.**
</limitations>
</chrome_devtools>

<ios_debugging>
## iOS Safari Debugging

<safari_web_inspector>
**Prerequisites:**
1. Mac with Safari
2. iPhone/iPad with iOS 14+
3. USB cable

**Setup:**
1. iPhone: Settings → Safari → Advanced → Web Inspector: ON
2. iPhone: Settings → Safari → Advanced → Remote Automation: ON (optional)
3. Mac: Safari → Settings → Advanced → Show Develop menu in menu bar

**Connect:**
1. Connect iPhone to Mac via USB
2. Open Safari on iPhone, navigate to your page
3. Mac Safari: Develop menu → [Your iPhone] → [Your page URL]

**Available tools:**
- Console
- Elements/DOM inspector
- Network
- Resources
- Timelines (Performance)
- Storage
</safari_web_inspector>

<remote_debugging>
**Without Mac (using ngrok/localtunnel):**
```bash
# Expose localhost to internet
npx ngrok http 3000

# Or
npx localtunnel --port 3000
```

Open the URL on your iPhone. Use Eruda for in-page debugging:
```html
<script src="https://cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
```
</remote_debugging>

<simulator>
**Using iOS Simulator (Mac only):**
```bash
# List available simulators
xcrun simctl list devices

# Boot a simulator
xcrun simctl boot "iPhone 15 Pro"

# Open Simulator app
open -a Simulator
```

Safari in Simulator appears in Safari Develop menu.
</simulator>
</ios_debugging>

<android_debugging>
## Android Chrome Debugging

<remote_debugging>
**Prerequisites:**
1. Android device with USB debugging enabled
2. Chrome on Android
3. Chrome on desktop

**Setup:**
1. Android: Settings → Developer Options → USB Debugging: ON
2. Connect device via USB
3. Desktop Chrome: navigate to `chrome://inspect`
4. Find your device and page, click "inspect"

**Features:**
- Full DevTools (Elements, Console, Network, etc.)
- Live editing
- Screencast mode
</remote_debugging>

<adb_wireless>
**Wireless debugging (Android 11+):**
1. Enable Wireless Debugging in Developer Options
2. `adb pair <ip>:<port>` (use pairing code from device)
3. `adb connect <ip>:<port>`
</adb_wireless>
</android_debugging>

<testing_checklist>
## Mobile Testing Checklist

<viewport_layout>
**Viewport & Layout:**
- [ ] No horizontal overflow
- [ ] Content readable without zooming
- [ ] Safe areas handled (notch, home indicator)
- [ ] Works in portrait and landscape
- [ ] 100vh/svh used correctly
</viewport_layout>

<touch>
**Touch & Interaction:**
- [ ] Touch targets 44px+ minimum
- [ ] No 300ms tap delay
- [ ] Scroll behavior smooth
- [ ] Forms usable with virtual keyboard
- [ ] Gestures work correctly
</touch>

<performance>
**Performance:**
- [ ] LCP < 2.5s on 3G
- [ ] INP < 200ms
- [ ] CLS < 0.1
- [ ] Images optimized (WebP/AVIF)
- [ ] JavaScript not blocking main thread
</performance>

<pwa>
**PWA:**
- [ ] Manifest valid (check in DevTools)
- [ ] Service worker registered
- [ ] Offline page works
- [ ] Icons loading correctly
- [ ] Installs on iOS (Add to Home Screen)
- [ ] Installs on Android (install prompt)
- [ ] Theme color applied
</pwa>

<cross_browser>
**Cross-Browser:**
- [ ] Safari iOS
- [ ] Chrome iOS (uses WebKit)
- [ ] Chrome Android
- [ ] Samsung Internet
- [ ] Firefox Android
</cross_browser>

<accessibility>
**Accessibility:**
- [ ] VoiceOver navigation works (iOS)
- [ ] TalkBack navigation works (Android)
- [ ] Focus order logical
- [ ] Color contrast sufficient
- [ ] Text resizable without breaking layout
</accessibility>
</testing_checklist>

<common_debugging_scenarios>
## Common Debugging Scenarios

<scenario name="CSS not working on iOS">
1. Check for Safari-specific bugs (see ios-safari-quirks.md)
2. Verify viewport meta tag is correct
3. Check for -webkit- prefix requirements
4. Test in Safari Web Inspector on real device
5. Compare behavior in Chrome iOS (also WebKit)
</scenario>

<scenario name="Layout breaks with keyboard">
1. Check for 100vh elements (use svh instead)
2. Verify fixed positioning behavior
3. Use visualViewport API if needed
4. Test with keyboard in both orientations
</scenario>

<scenario name="PWA not installing">
1. Check manifest in DevTools → Application → Manifest
2. Verify HTTPS (or localhost)
3. Confirm service worker registered
4. Check icon requirements (192x192, 512x512)
5. Ensure start_url is accessible
</scenario>

<scenario name="Touch feels unresponsive">
1. Check for 300ms delay (add touch-action: manipulation)
2. Look for heavy JavaScript blocking main thread
3. Profile with Performance tab
4. Check for passive event listener issues
</scenario>

<scenario name="Performance issues on mobile">
1. Run Lighthouse on Mobile
2. Check for large images (use Next.js Image)
3. Profile JavaScript with CPU throttling
4. Check for layout thrashing
5. Test on mid-range device, not just flagship
</scenario>
</common_debugging_scenarios>

<tools>
## Testing Tools & Services

| Tool | Purpose | Cost |
|------|---------|------|
| BrowserStack | Real device cloud testing | Paid |
| Sauce Labs | Cross-browser testing | Paid |
| LambdaTest | Browser testing | Paid |
| PageSpeed Insights | Performance testing | Free |
| WebPageTest | Detailed performance | Free |
| Eruda | In-page mobile console | Free |
| ngrok | Expose localhost | Free tier |
| web-vitals | Real user metrics | Free |
</tools>
