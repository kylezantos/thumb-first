<overview>
Service workers are JavaScript files that run in the background, separate from your web page. They enable offline functionality, push notifications, and background sync. For PWAs, service workers are required for installability and provide the foundation for offline-first experiences.
</overview>

<fundamentals>
## Service Worker Lifecycle

```
Download → Install → Waiting → Activate → Running
```

1. **Download:** Browser downloads the service worker file
2. **Install:** `install` event fires, cache initial assets
3. **Waiting:** New SW waits for old SW to release control
4. **Activate:** `activate` event fires, clean up old caches
5. **Running:** SW intercepts fetch requests
</fundamentals>

<nextjs_options>
## Next.js Service Worker Options

<option name="Native Service Worker (Recommended 2025)">
Next.js 14+ supports PWAs without additional packages.

Create `public/sw.js`:
```javascript
const CACHE_NAME = 'my-app-v1';
const OFFLINE_URL = '/offline';

const PRECACHE_ASSETS = [
  '/',
  '/offline',
  '/icons/icon-192.png',
  '/icons/icon-512.png',
];

// Install: cache essential assets
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.addAll(PRECACHE_ASSETS);
    })
  );
  self.skipWaiting();
});

// Activate: clean up old caches
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

// Fetch: network-first for pages, cache-first for assets
self.addEventListener('fetch', (event) => {
  const { request } = event;

  // Skip non-GET requests
  if (request.method !== 'GET') return;

  // Network-first for HTML pages
  if (request.mode === 'navigate') {
    event.respondWith(
      fetch(request)
        .then((response) => {
          // Cache successful responses
          const clone = response.clone();
          caches.open(CACHE_NAME).then((cache) => {
            cache.put(request, clone);
          });
          return response;
        })
        .catch(() => caches.match(OFFLINE_URL))
    );
    return;
  }

  // Cache-first for assets
  event.respondWith(
    caches.match(request).then((cached) => {
      if (cached) return cached;
      return fetch(request).then((response) => {
        // Cache new assets
        const clone = response.clone();
        caches.open(CACHE_NAME).then((cache) => {
          cache.put(request, clone);
        });
        return response;
      });
    })
  );
});
```
</option>

<option name="Serwist (next-pwa successor)">
The modern replacement for next-pwa:

```bash
pnpm add @serwist/next @serwist/precaching @serwist/strategies
```

```javascript
// next.config.js
const withSerwist = require("@serwist/next").default({
  swSrc: "app/sw.ts",
  swDest: "public/sw.js",
  disable: process.env.NODE_ENV === "development",
});

module.exports = withSerwist({
  // Your Next.js config
});
```

```typescript
// app/sw.ts
import { defaultCache } from "@serwist/next/worker";
import { Serwist } from "serwist";

const serwist = new Serwist({
  precacheEntries: self.__SW_MANIFEST,
  skipWaiting: true,
  clientsClaim: true,
  navigationPreload: true,
  runtimeCaching: defaultCache,
});

serwist.addEventListeners();
```
</option>

<option name="next-pwa (Legacy - Not Recommended)">
**Note:** next-pwa is unmaintained since July 2024. Use Serwist instead.

If you must use it:
```javascript
const withPWA = require('next-pwa')({
  dest: 'public',
  disable: process.env.NODE_ENV === 'development',
});
```
</option>
</nextjs_options>

<registration>
## Service Worker Registration

```tsx
// components/ServiceWorkerRegistration.tsx
'use client';

import { useEffect } from 'react';

export function ServiceWorkerRegistration() {
  useEffect(() => {
    if ('serviceWorker' in navigator && process.env.NODE_ENV === 'production') {
      navigator.serviceWorker
        .register('/sw.js')
        .then((registration) => {
          console.log('SW registered:', registration.scope);

          // Check for updates periodically
          setInterval(() => {
            registration.update();
          }, 60 * 60 * 1000); // Every hour
        })
        .catch((error) => {
          console.error('SW registration failed:', error);
        });
    }
  }, []);

  return null;
}
```

Add to layout:
```tsx
// app/layout.tsx
import { ServiceWorkerRegistration } from '@/components/ServiceWorkerRegistration';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <ServiceWorkerRegistration />
      </body>
    </html>
  );
}
```
</registration>

<caching_strategies>
## Caching Strategies

<strategy name="Cache First (Cache, falling back to network)">
Best for: Static assets (images, fonts, CSS, JS)

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((cached) => {
      return cached || fetch(event.request);
    })
  );
});
```

**Pros:** Fast, works offline
**Cons:** May serve stale content
</strategy>

<strategy name="Network First (Network, falling back to cache)">
Best for: HTML pages, API data that needs freshness

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    fetch(event.request)
      .then((response) => {
        // Cache the fresh response
        const clone = response.clone();
        caches.open(CACHE_NAME).then((cache) => {
          cache.put(event.request, clone);
        });
        return response;
      })
      .catch(() => caches.match(event.request))
  );
});
```

**Pros:** Always fresh when online
**Cons:** Slow on poor connections
</strategy>

<strategy name="Stale While Revalidate">
Best for: Content that updates but staleness is acceptable (avatars, article content)

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.match(event.request).then((cached) => {
        const fetchPromise = fetch(event.request).then((response) => {
          cache.put(event.request, response.clone());
          return response;
        });
        return cached || fetchPromise;
      });
    })
  );
});
```

**Pros:** Fast response, eventually consistent
**Cons:** May show stale data briefly
</strategy>

<strategy name="Cache Only">
Best for: Pre-cached app shell assets

```javascript
event.respondWith(caches.match(event.request));
```
</strategy>

<strategy name="Network Only">
Best for: Real-time data, analytics, non-cacheable requests

```javascript
event.respondWith(fetch(event.request));
```
</strategy>
</caching_strategies>

<strategy_by_resource>
## Recommended Strategy by Resource Type

| Resource Type | Strategy | Rationale |
|--------------|----------|-----------|
| HTML pages | Network First | Need fresh content, fallback offline |
| App shell (layout) | Cache First | Rarely changes, fast loading |
| CSS/JS bundles | Cache First with version | Hashed filenames handle updates |
| Images | Cache First | Rarely change, save bandwidth |
| Fonts | Cache First | Never change |
| API (GET) | Stale While Revalidate | Balance freshness and speed |
| API (POST/PUT) | Network Only | Can't cache mutations |
| Analytics | Network Only | Must reach server |
</strategy_by_resource>

<cache_versioning>
## Cache Versioning

Always version your cache to handle updates:

```javascript
const CACHE_VERSION = 'v2'; // Increment on deploy
const CACHE_NAME = `my-app-${CACHE_VERSION}`;

self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name.startsWith('my-app-') && name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    })
  );
});
```
</cache_versioning>

<offline_page>
## Offline Fallback Page

Create an offline page that's cached on install:

```tsx
// app/offline/page.tsx
export default function OfflinePage() {
  return (
    <div className="flex min-h-svh items-center justify-center p-4">
      <div className="text-center max-w-md">
        <h1 className="text-2xl font-bold mb-4">You're Offline</h1>
        <p className="text-muted-foreground mb-6">
          Please check your internet connection and try again.
        </p>
        <button
          onClick={() => window.location.reload()}
          className="px-4 py-2 bg-primary text-primary-foreground rounded-lg"
        >
          Try Again
        </button>
      </div>
    </div>
  );
}
```

Pre-cache in service worker:
```javascript
const PRECACHE = ['/', '/offline'];
```
</offline_page>

<ios_limitations>
## iOS Service Worker Limitations

1. **50MB storage limit** - Cache + IndexedDB combined
2. **No background sync** - Can't sync when app is closed
3. **No periodic background fetch** - No scheduled updates
4. **7-day data expiry** - If PWA not installed, data may be cleared
5. **No push when closed** - Push only works when PWA is in foreground or receives push

**Workarounds:**
- Encourage users to install PWA to home screen
- Keep cache size small
- Store important data server-side
- Sync data on app open
</ios_limitations>

<debugging>
## Debugging Service Workers

**Chrome DevTools:**
1. Application tab → Service Workers
2. Check "Update on reload" for development
3. View cache contents in "Cache Storage"

**Unregister for testing:**
```javascript
navigator.serviceWorker.getRegistrations().then((registrations) => {
  registrations.forEach((registration) => {
    registration.unregister();
  });
});
```

**Force update:**
```javascript
navigator.serviceWorker.ready.then((registration) => {
  registration.update();
});
```
</debugging>
