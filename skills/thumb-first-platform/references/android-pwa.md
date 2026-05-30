<overview>
Android provides significantly better PWA support than iOS through Chrome and Chromium-based browsers. Key advantages include automatic install prompts, WebAPK installation, and full Web Push support. Understanding Android PWA behavior helps build cross-platform experiences.
</overview>

<browser_support>
## Browser Support on Android

| Browser | Engine | PWA Support | Market Share |
|---------|--------|-------------|--------------|
| Chrome | Chromium | Full | ~65% |
| Samsung Internet | Chromium | Full | ~15% |
| Firefox | Gecko | Good | ~3% |
| Edge | Chromium | Full | ~2% |
| Opera | Chromium | Full | ~2% |

**Key difference from iOS:** Android browsers use their own engines. Chrome uses Chromium, Firefox uses Gecko. This is unlike iOS where all browsers must use WebKit.
</browser_support>

<installation>
## PWA Installation on Android

<feature name="Install Prompt">
Android Chrome shows an automatic install prompt when PWA criteria are met:
- Served over HTTPS
- Valid manifest.json with required fields
- Service worker with fetch handler
- User has engaged with the site

```javascript
// Listen for install prompt
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  e.preventDefault();
  deferredPrompt = e;
  // Show your custom install button
  showInstallButton();
});

// Trigger install when user clicks your button
installButton.addEventListener('click', async () => {
  deferredPrompt.prompt();
  const { outcome } = await deferredPrompt.userChoice;
  console.log(`User ${outcome === 'accepted' ? 'accepted' : 'dismissed'} install`);
  deferredPrompt = null;
});
```
</feature>

<feature name="WebAPK">
On devices with Google Play Services (most Android devices), Chrome creates a WebAPK - a real Android package that:
- Appears in app drawer and settings like native app
- Has its own process and icon
- Receives OS-level updates
- Can be uninstalled normally

**Samsung Internet** also creates APKs through Galaxy Store infrastructure.
</feature>

<feature name="Install Criteria">
Minimum requirements for installability:
- HTTPS (or localhost for development)
- manifest.json with:
  - `name` or `short_name`
  - `start_url`
  - `display: 'standalone'` or `'fullscreen'` or `'minimal-ui'`
  - Icons: 192x192 and 512x512 pixels
- Service worker with fetch event handler
</feature>
</installation>

<push_notifications>
## Push Notifications on Android

**Full support** - works in browser and installed PWA, foreground and background.

```javascript
// Request permission
const permission = await Notification.requestPermission();

if (permission === 'granted') {
  // Get push subscription
  const registration = await navigator.serviceWorker.ready;
  const subscription = await registration.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: urlBase64ToUint8Array(VAPID_PUBLIC_KEY)
  });

  // Send subscription to your server
  await sendToServer(subscription);
}
```

**Key differences from iOS:**
- Works without installing to home screen
- Background delivery works reliably
- No special requirements beyond HTTPS and service worker
</push_notifications>

<theming>
## Android UI Theming

<feature name="Theme Color">
The `theme_color` in manifest.json colors the Android address bar and task switcher:

```json
{
  "theme_color": "#4285f4"
}
```

Can also be set via meta tag (allows dynamic updates):
```html
<meta name="theme-color" content="#4285f4">
```

```javascript
// Dynamic theme color
document.querySelector('meta[name="theme-color"]')
  .setAttribute('content', isDark ? '#1a1a1a' : '#ffffff');
```
</feature>

<feature name="Splash Screen">
Android automatically generates splash screen from manifest:
- Uses `background_color` for splash background
- Centers the icon from `icons` array
- Displays `name` below icon

```json
{
  "name": "My App",
  "background_color": "#ffffff",
  "theme_color": "#4285f4",
  "icons": [
    {
      "src": "/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```
</feature>

<feature name="Display Modes">
```json
{
  "display": "standalone"  // Most app-like
  // Other options:
  // "fullscreen" - no status bar (games)
  // "minimal-ui" - some browser controls
  // "browser" - regular browser tab
}
```
</feature>
</theming>

<differences_from_ios>
## Key Differences from iOS

| Feature | Android | iOS |
|---------|---------|-----|
| Install prompt | Automatic | Manual (Share → Add to Home) |
| Push notifications | Works everywhere | Only installed PWAs, iOS 16.4+ |
| Background sync | Supported | Not supported |
| Storage limits | More generous | 50MB cache limit |
| Storage persistence | Persists | 7-day cap if not installed |
| Browser engines | Multiple (Chromium, Gecko) | WebKit only |
| Install location | App drawer + home screen | Home screen only |
| Uninstall | Normal app uninstall | Delete from home screen |

**Practical implications:**
- Test iOS more thoroughly - it has more limitations
- Don't assume features that work on Android will work on iOS
- Consider iOS as the minimum baseline for PWA features
</differences_from_ios>

<samsung_internet>
## Samsung Internet Specifics

Samsung Internet has ~15% market share on Android, especially on Samsung devices.

**Key features:**
- DeX mode support for desktop-like PWA experience
- Samsung Pay integration
- Biometric authentication API
- Ad blocking built-in (may affect analytics)

**PWA differences:**
- Uses Galaxy Store for PWA installation on some devices
- May have slight differences in Web API support
- Test specifically if targeting Samsung users
</samsung_internet>

<testing>
## Testing Android PWAs

<method name="Chrome Remote Debugging">
1. Enable Developer Options on Android
2. Enable USB Debugging
3. Connect via USB
4. Open `chrome://inspect` in desktop Chrome
5. Click "inspect" next to your app
</method>

<method name="ADB Wireless">
```bash
# First time (with USB)
adb tcpip 5555
adb connect <device-ip>:5555

# Then disconnect USB, debug wirelessly
```
</method>

<method name="Lighthouse CI">
```bash
# Run Lighthouse against deployed URL
npx lighthouse https://your-pwa.com \
  --preset=perf \
  --emulated-form-factor=mobile \
  --output=html
```
</method>
</testing>

<best_practices>
## Best Practices for Android PWAs

1. **Always provide both 192x192 and 512x512 icons** - required for install prompt
2. **Include maskable icon** - looks better on Android adaptive icons
3. **Set theme_color** - colors address bar and improves branding
4. **Use display: standalone** - most app-like experience
5. **Implement custom install UI** - capture beforeinstallprompt event
6. **Test on Samsung Internet** - significant market share
7. **Don't forget Firefox** - uses different engine, may have quirks
</best_practices>
