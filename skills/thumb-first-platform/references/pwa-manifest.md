<overview>
The Web App Manifest (manifest.json) is a JSON file that provides metadata about your PWA. It controls how the app appears when installed, including name, icons, splash screen, and display mode. Proper manifest configuration is required for PWA installability.
</overview>

<nextjs_manifest>
## Next.js Manifest Configuration

<method name="TypeScript Manifest (Recommended)">
```typescript
// app/manifest.ts
import type { MetadataRoute } from 'next'

export default function manifest(): MetadataRoute.Manifest {
  return {
    name: 'My Progressive Web App',
    short_name: 'MyPWA',
    description: 'A description of your app',
    start_url: '/',
    display: 'standalone',
    background_color: '#ffffff',
    theme_color: '#000000',
    orientation: 'portrait-primary',
    icons: [
      {
        src: '/icons/icon-192.png',
        sizes: '192x192',
        type: 'image/png',
      },
      {
        src: '/icons/icon-512.png',
        sizes: '512x512',
        type: 'image/png',
      },
      {
        src: '/icons/icon-maskable-512.png',
        sizes: '512x512',
        type: 'image/png',
        purpose: 'maskable',
      },
    ],
  }
}
```
</method>

<method name="JSON Manifest">
```json
// public/manifest.json
{
  "name": "My Progressive Web App",
  "short_name": "MyPWA",
  "description": "A description of your app",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "orientation": "portrait-primary",
  "icons": [
    {
      "src": "/icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-maskable-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable"
    }
  ]
}
```
Link in HTML:
```html
<link rel="manifest" href="/manifest.json">
```
</method>
</nextjs_manifest>

<required_fields>
## Required Fields for Installability

| Field | Requirement | Notes |
|-------|-------------|-------|
| `name` or `short_name` | Required | At least one must be present |
| `start_url` | Required | Entry point when app opens |
| `display` | Required | Must be `standalone`, `fullscreen`, or `minimal-ui` |
| `icons` | Required | At least 192x192 and 512x512 |

**Chrome-specific:** Also requires a registered service worker with fetch handler.
</required_fields>

<fields_reference>
## All Manifest Fields

<field name="name">
Full name of the application. Used in app install dialog and home screen if space allows.
```json
"name": "My Progressive Web Application"
```
</field>

<field name="short_name">
Short name for limited space. **Keep under 12 characters** to avoid truncation.
```json
"short_name": "MyPWA"
```
</field>

<field name="description">
Description shown in app stores and install dialogs.
```json
"description": "A fast, reliable app for managing your tasks"
```
</field>

<field name="start_url">
URL that opens when app launches. Should be your main app screen, not a marketing page.
```json
"start_url": "/"
```
Can include query params for analytics:
```json
"start_url": "/?source=pwa"
```
</field>

<field name="display">
How the app displays when launched:
- `standalone` - Most app-like, no browser UI (recommended)
- `fullscreen` - No browser UI, no status bar (games)
- `minimal-ui` - Some browser controls visible
- `browser` - Regular browser tab
```json
"display": "standalone"
```
</field>

<field name="background_color">
Background color for splash screen. Should match your app's background.
```json
"background_color": "#ffffff"
```
</field>

<field name="theme_color">
Colors browser address bar (Android) and status bar area.
```json
"theme_color": "#4285f4"
```
Can also set via meta tag for dynamic updates:
```html
<meta name="theme-color" content="#4285f4">
```
</field>

<field name="orientation">
Preferred orientation:
- `portrait-primary` - Portrait, home button at bottom
- `landscape-primary` - Landscape
- `any` - Follow device orientation
```json
"orientation": "portrait-primary"
```
</field>

<field name="scope">
Navigation scope. URLs outside scope open in browser.
```json
"scope": "/app/"
```
Default is the directory containing the manifest.
</field>

<field name="lang">
Primary language.
```json
"lang": "en-US"
```
</field>

<field name="dir">
Text direction: `ltr`, `rtl`, or `auto`.
```json
"dir": "ltr"
```
</field>
</fields_reference>

<icons_configuration>
## Icons Configuration

<requirement name="Minimum Required">
```json
"icons": [
  {
    "src": "/icons/icon-192.png",
    "sizes": "192x192",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-512.png",
    "sizes": "512x512",
    "type": "image/png"
  }
]
```
</requirement>

<recommendation name="Full Icon Set">
```json
"icons": [
  {
    "src": "/icons/icon-72.png",
    "sizes": "72x72",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-96.png",
    "sizes": "96x96",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-128.png",
    "sizes": "128x128",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-144.png",
    "sizes": "144x144",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-152.png",
    "sizes": "152x152",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-192.png",
    "sizes": "192x192",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-384.png",
    "sizes": "384x384",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-512.png",
    "sizes": "512x512",
    "type": "image/png"
  },
  {
    "src": "/icons/icon-maskable-512.png",
    "sizes": "512x512",
    "type": "image/png",
    "purpose": "maskable"
  }
]
```
</recommendation>

<concept name="Maskable Icons">
Maskable icons are designed for Android adaptive icons. They have a "safe zone" in the center and can be cropped to circles, squares, or rounded rectangles.

```json
{
  "src": "/icons/icon-maskable-512.png",
  "sizes": "512x512",
  "type": "image/png",
  "purpose": "maskable"
}
```

**Design guidelines:**
- Important content in center 80% (safe zone)
- Full bleed background
- Test with maskable icon tool: https://maskable.app/
</concept>

<concept name="SVG Icons">
For crisp icons at any size:
```json
{
  "src": "/icons/icon.svg",
  "sizes": "any",
  "type": "image/svg+xml"
}
```
Always include PNG fallback for older browsers.
</concept>
</icons_configuration>

<ios_specific>
## iOS-Specific Configuration

iOS requires additional HTML meta tags and link elements:

```html
<!-- Enable standalone mode -->
<meta name="apple-mobile-web-app-capable" content="yes">

<!-- Status bar style -->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">

<!-- App title (separate from manifest) -->
<meta name="apple-mobile-web-app-title" content="MyPWA">

<!-- Apple Touch Icon -->
<link rel="apple-touch-icon" href="/icons/apple-touch-icon.png">

<!-- Multiple sizes for different devices -->
<link rel="apple-touch-icon" sizes="180x180" href="/icons/apple-touch-icon-180.png">
<link rel="apple-touch-icon" sizes="152x152" href="/icons/apple-touch-icon-152.png">
<link rel="apple-touch-icon" sizes="120x120" href="/icons/apple-touch-icon-120.png">
```

**Next.js metadata API:**
```typescript
export const metadata: Metadata = {
  appleWebApp: {
    capable: true,
    statusBarStyle: 'black-translucent',
    title: 'MyPWA',
  },
}
```
</ios_specific>

<splash_screens>
## iOS Splash Screens

iOS doesn't auto-generate splash screens from manifest. You need specific images:

```html
<!-- iPhone 15 Pro Max, 14 Pro Max -->
<link rel="apple-touch-startup-image"
      href="/splash/apple-splash-1290-2796.png"
      media="(device-width: 430px) and (device-height: 932px) and (-webkit-device-pixel-ratio: 3)">

<!-- iPhone 15 Pro, 14 Pro -->
<link rel="apple-touch-startup-image"
      href="/splash/apple-splash-1179-2556.png"
      media="(device-width: 393px) and (device-height: 852px) and (-webkit-device-pixel-ratio: 3)">

<!-- ... many more for each device/orientation -->
```

**Generate automatically:**
```bash
npx pwa-asset-generator ./logo.png ./public/splash \
  --splash-only \
  --type png
```
</splash_screens>

<validation>
## Validating Your Manifest

**Chrome DevTools:**
1. Open DevTools → Application tab
2. Click "Manifest" in sidebar
3. Check for errors and warnings

**Lighthouse:**
1. DevTools → Lighthouse
2. Check "Progressive Web App"
3. Run audit

**Common errors:**
- Missing icons (need 192 and 512)
- Invalid `display` value
- `start_url` not within `scope`
- Icons return 404
- Not served over HTTPS
</validation>

<generation_tools>
## Icon Generation Tools

**PWA Asset Generator (Recommended):**
```bash
npx pwa-asset-generator ./logo.png ./public/icons
```
Generates all icon sizes and HTML tags.

**Maskable.app:**
https://maskable.app/editor
Preview and create maskable icons.

**RealFaviconGenerator:**
https://realfavicongenerator.net/
Web-based, generates full favicon set.
</generation_tools>
