# Workflow: Test Mobile Compatibility

<required_reading>
**Read these reference files before testing:**
1. references/testing-debugging.md
2. references/ios-safari-quirks.md
</required_reading>

<process>
## Step 1: Automated Testing with Lighthouse

**Run Lighthouse audit:**
```
DevTools → Lighthouse → Select "Mobile" → Check all categories → Analyze
```

**Key scores to check:**
- Performance: 90+ target
- Accessibility: 90+ target
- Best Practices: 90+ target
- SEO: 90+ target
- PWA: All checks should pass

**Save report for comparison:**
```bash
# CLI option for CI/CD
npx lighthouse https://your-app.com --preset=perf --output=html --output-path=./lighthouse-report.html
```

## Step 2: Chrome DevTools Mobile Simulation

**Enable device mode:**
```
DevTools → Toggle Device Toolbar (Cmd+Shift+M / Ctrl+Shift+M)
```

**Test these viewports:**
- iPhone SE (375×667) - smallest common iPhone
- iPhone 14 Pro (393×852) - with Dynamic Island
- iPhone 14 Pro Max (430×932) - largest iPhone
- Pixel 7 (412×915) - common Android
- iPad Mini (768×1024) - tablet breakpoint
- iPad Pro (1024×1366) - large tablet

**Test orientations:**
- Portrait and landscape for each device

**Simulate network conditions:**
```
DevTools → Network tab → Throttling dropdown → Slow 3G
```

**Important:** DevTools simulation does NOT accurately represent:
- iOS Safari CSS bugs
- Real touch behavior
- Safe area insets
- Address bar behavior
- Actual performance on mobile hardware

## Step 3: Test on Real iOS Device

**Option A: Direct USB connection (recommended)**
1. Connect iPhone to Mac with cable
2. iPhone: Settings → Safari → Advanced → Web Inspector ON
3. Open Safari on iPhone, navigate to your app
4. Mac: Safari → Develop → [Your iPhone] → [Your page]
5. Use Web Inspector to debug

**Option B: Remote debugging via ngrok/localtunnel**
```bash
# Start local dev server
pnpm dev

# In another terminal, expose localhost
npx ngrok http 3000
# or
npx localtunnel --port 3000
```
Open the provided URL on your iPhone.

**iOS-specific tests:**
- [ ] Safe areas on iPhone with notch/Dynamic Island
- [ ] Safe areas in landscape orientation
- [ ] 100vh/svh behavior - no overflow
- [ ] Touch targets are 44pt minimum
- [ ] Tap responsiveness (no 300ms delay)
- [ ] Keyboard behavior with fixed elements
- [ ] Rubber band scroll at page boundaries
- [ ] PWA installation from Safari share menu
- [ ] PWA opens in standalone mode
- [ ] PWA splash screen appears
- [ ] Status bar style correct in PWA mode

## Step 4: Test on Real Android Device

**Option A: Chrome Remote Debugging**
1. Android: Settings → Developer Options → USB Debugging ON
2. Connect Android to computer with cable
3. Open Chrome on Android, navigate to your app
4. Computer: chrome://inspect → Click "inspect" next to your page

**Option B: ADB over WiFi**
```bash
adb connect <device-ip>:5555
```
Then use chrome://inspect.

**Android-specific tests:**
- [ ] Theme color applies to address bar
- [ ] Install prompt appears (if criteria met)
- [ ] PWA installs with correct icon
- [ ] PWA splash screen correct
- [ ] Push notifications work (if implemented)
- [ ] Back button behavior in PWA
- [ ] WebAPK installation successful

## Step 5: Cross-Browser Testing

**Browsers to test:**
- Safari (iOS) - uses WebKit
- Chrome (iOS) - also uses WebKit on iOS!
- Chrome (Android) - Chromium
- Samsung Internet - Chromium-based
- Firefox (Android) - Gecko

**Use BrowserStack or Sauce Labs for:**
- Older iOS versions (14, 15, 16)
- Older Android versions
- Specific device models you don't own

## Step 6: Accessibility Testing

**Automated:**
```
DevTools → Lighthouse → Accessibility audit
```

**Manual on iOS:**
1. Settings → Accessibility → VoiceOver → ON
2. Navigate your app with VoiceOver
3. Ensure all interactive elements are announced

**Manual on Android:**
1. Settings → Accessibility → TalkBack → ON
2. Navigate your app with TalkBack
3. Check touch target sizes (48dp minimum for Android)

## Step 7: Performance Testing on Real Devices

**Why real device testing matters:**
- DevTools throttling doesn't match real mobile CPUs
- Real devices have memory constraints
- Touch responsiveness differs
- GPU capabilities vary

**Test on mid-range device:**
Don't just test on flagship phones. Use:
- iPhone SE (2nd/3rd gen) for iOS
- Budget Android device (< $300) for Android

**Measure real metrics:**
```tsx
// Add to your app to log real-user metrics
import { onLCP, onINP, onCLS } from 'web-vitals'

onLCP(console.log)
onINP(console.log)
onCLS(console.log)
```

## Step 8: PWA-Specific Testing

**Service Worker Testing:**
```
DevTools → Application → Service Workers
```
- [ ] Service worker registered and activated
- [ ] No errors in console
- [ ] Cache storage contains expected files
- [ ] "Update on reload" works for testing

**Offline Testing:**
```
DevTools → Network → Offline checkbox
```
- [ ] Offline page displays
- [ ] Cached pages still load
- [ ] Meaningful offline experience

**Installation Testing:**
```
DevTools → Application → Manifest
```
- [ ] No manifest errors
- [ ] Icons loading correctly
- [ ] Install prompt appears (Chrome)
- [ ] Installable on iOS via Share menu
</process>

<anti_patterns>
**Avoid:**
- Testing only in Chrome DevTools emulator
- Skipping real iOS device testing
- Only testing on flagship devices
- Ignoring landscape orientation
- Not testing with keyboard open
- Assuming Android behavior matches iOS
- Skipping accessibility testing
</anti_patterns>

<success_criteria>
Mobile testing is complete when:
- [ ] Lighthouse scores recorded for all categories
- [ ] Tested on at least one real iOS device
- [ ] Tested on at least one real Android device
- [ ] Tested in multiple browsers
- [ ] Safe areas verified in portrait and landscape
- [ ] Touch interactions verified
- [ ] PWA installation verified on both platforms
- [ ] Offline behavior verified
- [ ] Accessibility tested with screen reader
- [ ] Performance tested on mid-range device
</success_criteria>
