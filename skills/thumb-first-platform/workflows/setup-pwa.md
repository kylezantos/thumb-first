# Workflow: Set Up PWA

<required_reading>
**Read these reference files before setup:**
1. references/pwa-manifest.md
2. references/service-workers.md
3. references/ios-safari-quirks.md
</required_reading>

<process>
## Step 1: Create Web App Manifest

For Next.js 14+, create `app/manifest.ts` (or `manifest.json` in `public/`):

```typescript
// app/manifest.ts
import type { MetadataRoute } from 'next'

export default function manifest(): MetadataRoute.Manifest {
  return {
    name: 'Your App Name',
    short_name: 'AppName', // Max 12 characters
    description: 'Your app description',
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

## Step 2: Generate PWA Icons

**Required icon sizes:**
- 192x192 (required for Chrome)
- 512x512 (required for Chrome)
- 180x180 (Apple Touch Icon)
- Maskable version (for Android adaptive icons)

**Generate with:**
```bash
# Using pwa-asset-generator (recommended)
npx pwa-asset-generator ./logo.png ./public/icons --index ./app/layout.tsx
```

**Or manually create:**
- `/public/icons/icon-192.png`
- `/public/icons/icon-512.png`
- `/public/icons/icon-maskable-512.png`
- `/public/icons/apple-touch-icon.png` (180x180)

## Step 3: Add Meta Tags for iOS

In your layout.tsx or _document.tsx:

```tsx
// app/layout.tsx
export const metadata: Metadata = {
  // ... other metadata
  appleWebApp: {
    capable: true,
    statusBarStyle: 'black-translucent',
    title: 'Your App',
  },
  formatDetection: {
    telephone: false, // Prevent auto-linking phone numbers
  },
}

// Or in head
<>
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <meta name="apple-mobile-web-app-title" content="Your App" />
  <link rel="apple-touch-icon" href="/icons/apple-touch-icon.png" />
</>
```

## Step 4: Create Service Worker

**Option A: Next.js built-in (simple, recommended for 2025)**

Next.js 14+ supports PWA natively. Add to `next.config.js`:

```javascript
// next.config.js
const nextConfig = {
  // Enable PWA features
  experimental: {
    // If using App Router, this is typically sufficient
  },
}
```

Create a basic service worker at `public/sw.js`:

```javascript
const CACHE_NAME = 'app-v1';
const OFFLINE_URL = '/offline';

// Assets to cache immediately
const PRECACHE_ASSETS = [
  '/',
  '/offline',
  '/icons/icon-192.png',
  '/icons/icon-512.png',
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.addAll(PRECACHE_ASSETS);
    })
  );
  self.skipWaiting();
});

self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    })
  );
  self.clients.claim();
});

self.addEventListener('fetch', (event) => {
  // Network-first for HTML, cache-first for assets
  if (event.request.mode === 'navigate') {
    event.respondWith(
      fetch(event.request).catch(() => {
        return caches.match(OFFLINE_URL);
      })
    );
  } else {
    event.respondWith(
      caches.match(event.request).then((response) => {
        return response || fetch(event.request);
      })
    );
  }
});
```

**Option B: Using Serwist (next-pwa successor)**

```bash
pnpm add @serwist/next
```

```javascript
// next.config.js
const withSerwist = require("@serwist/next").default({
  swSrc: "app/sw.ts",
  swDest: "public/sw.js",
});

module.exports = withSerwist({
  // Your Next.js config
});
```

## Step 5: Register Service Worker

```tsx
// components/ServiceWorkerRegistration.tsx
'use client'

import { useEffect } from 'react'

export function ServiceWorkerRegistration() {
  useEffect(() => {
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker
        .register('/sw.js')
        .then((registration) => {
          console.log('SW registered:', registration.scope)
        })
        .catch((error) => {
          console.log('SW registration failed:', error)
        })
    }
  }, [])

  return null
}

// Add to layout.tsx
<ServiceWorkerRegistration />
```

## Step 6: Create Offline Page

```tsx
// app/offline/page.tsx
export default function OfflinePage() {
  return (
    <div className="flex min-h-svh items-center justify-center p-4">
      <div className="text-center">
        <h1 className="text-2xl font-bold mb-4">You're Offline</h1>
        <p className="text-muted-foreground">
          Please check your internet connection and try again.
        </p>
        <button
          onClick={() => window.location.reload()}
          className="mt-4 px-4 py-2 bg-primary text-primary-foreground rounded"
        >
          Retry
        </button>
      </div>
    </div>
  )
}
```

## Step 7: iOS Splash Screens (Optional)

iOS requires specific splash screen images. Generate with:

```bash
npx pwa-asset-generator ./logo.png ./public/splash \
  --splash-only \
  --type png \
  --index ./app/layout.tsx
```

This generates `<link rel="apple-touch-startup-image">` tags for all iOS screen sizes.

## Step 8: Test PWA Installation

**Chrome (Android/Desktop):**
1. Open DevTools → Application → Manifest
2. Check for errors
3. Look for "Install" button in address bar

**Safari (iOS):**
1. Open your app in Safari
2. Tap Share button
3. Tap "Add to Home Screen"
4. Verify icon and name appear correctly
5. Open from home screen - should be in standalone mode

**Lighthouse PWA Audit:**
1. DevTools → Lighthouse
2. Check "Progressive Web App"
3. Generate report
4. Fix any failing checks
</process>

<anti_patterns>
**Avoid:**
- Using deprecated `apple-mobile-web-app-capable` without manifest
- Forgetting maskable icons (Android adaptive icons look bad)
- Large service worker precache (keep under 50MB for iOS)
- Not versioning your cache (causes stale content)
- Using next-pwa (unmaintained since July 2024)
</anti_patterns>

<success_criteria>
PWA setup is complete when:
- [ ] Manifest loads without errors in DevTools
- [ ] All required icons present and loading
- [ ] Service worker registers and activates
- [ ] Offline page works when disconnected
- [ ] App installs from Chrome address bar
- [ ] App installs from Safari "Add to Home Screen"
- [ ] Lighthouse PWA audit passes all checks
- [ ] Splash screen displays on iOS
</success_criteria>
