<overview>
Web Push notifications allow PWAs to send notifications to users even when the app isn't open (on supported platforms). This requires a service worker, VAPID keys for authentication, and proper permission handling. iOS support is limited compared to Android.
</overview>

<platform_support>
## Platform Support (2024-2025)

| Platform | Support | Requirements |
|----------|---------|--------------|
| Chrome (Android) | Full | Service worker, HTTPS |
| Chrome (Desktop) | Full | Service worker, HTTPS |
| Safari (macOS) | Full | Service worker, HTTPS |
| Safari (iOS 16.4+) | Limited | **Must be installed to home screen** |
| Firefox | Full | Service worker, HTTPS |
| Samsung Internet | Full | Service worker, HTTPS |

**iOS Limitations:**
- Only works when PWA is installed to home screen
- No badge API
- No background push when app is completely closed
- Requires explicit permission after install
</platform_support>

<vapid_keys>
## VAPID Keys

VAPID (Voluntary Application Server Identification) authenticates your server to push services.

<generating>
**Generate keys:**
```bash
# Using web-push package
npm install -g web-push
web-push generate-vapid-keys
```

Output:
```
Public Key: BNbxg...
Private Key: dGVzd...
```

**Store securely:**
```env
# .env.local
NEXT_PUBLIC_VAPID_PUBLIC_KEY=BNbxg...
VAPID_PRIVATE_KEY=dGVzd...
VAPID_SUBJECT=mailto:your@email.com
```
</generating>
</vapid_keys>

<implementation>
## Implementation

<step name="1. Request Permission">
```tsx
// components/PushNotificationManager.tsx
'use client';

import { useState, useEffect } from 'react';

export function PushNotificationManager() {
  const [permission, setPermission] = useState<NotificationPermission>('default');
  const [subscription, setSubscription] = useState<PushSubscription | null>(null);

  useEffect(() => {
    if ('Notification' in window) {
      setPermission(Notification.permission);
    }
  }, []);

  const requestPermission = async () => {
    if (!('Notification' in window)) {
      alert('Notifications not supported');
      return;
    }

    const result = await Notification.requestPermission();
    setPermission(result);

    if (result === 'granted') {
      await subscribeToPush();
    }
  };

  const subscribeToPush = async () => {
    try {
      const registration = await navigator.serviceWorker.ready;

      const subscription = await registration.pushManager.subscribe({
        userVisibleOnly: true,
        applicationServerKey: urlBase64ToUint8Array(
          process.env.NEXT_PUBLIC_VAPID_PUBLIC_KEY!
        ),
      });

      setSubscription(subscription);

      // Send to your server
      await fetch('/api/push/subscribe', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(subscription),
      });
    } catch (error) {
      console.error('Push subscription failed:', error);
    }
  };

  return (
    <div>
      {permission === 'default' && (
        <button onClick={requestPermission}>
          Enable Notifications
        </button>
      )}
      {permission === 'granted' && <p>Notifications enabled</p>}
      {permission === 'denied' && <p>Notifications blocked</p>}
    </div>
  );
}

// Helper function
function urlBase64ToUint8Array(base64String: string): Uint8Array {
  const padding = '='.repeat((4 - (base64String.length % 4)) % 4);
  const base64 = (base64String + padding)
    .replace(/-/g, '+')
    .replace(/_/g, '/');
  const rawData = window.atob(base64);
  return Uint8Array.from([...rawData].map((char) => char.charCodeAt(0)));
}
```
</step>

<step name="2. Handle Push in Service Worker">
```javascript
// public/sw.js

self.addEventListener('push', (event) => {
  if (!event.data) return;

  const data = event.data.json();

  const options = {
    body: data.body,
    icon: '/icons/icon-192.png',
    badge: '/icons/badge-72.png',
    vibrate: [100, 50, 100],
    data: {
      url: data.url || '/',
    },
    actions: data.actions || [],
  };

  event.waitUntil(
    self.registration.showNotification(data.title, options)
  );
});

// Handle notification click
self.addEventListener('notificationclick', (event) => {
  event.notification.close();

  const url = event.notification.data.url;

  event.waitUntil(
    clients.matchAll({ type: 'window' }).then((clientList) => {
      // Focus existing window if open
      for (const client of clientList) {
        if (client.url === url && 'focus' in client) {
          return client.focus();
        }
      }
      // Open new window
      if (clients.openWindow) {
        return clients.openWindow(url);
      }
    })
  );
});
```
</step>

<step name="3. Server-Side Push (API Route)">
```typescript
// app/api/push/send/route.ts
import webpush from 'web-push';
import { NextResponse } from 'next/server';

webpush.setVapidDetails(
  process.env.VAPID_SUBJECT!,
  process.env.NEXT_PUBLIC_VAPID_PUBLIC_KEY!,
  process.env.VAPID_PRIVATE_KEY!
);

export async function POST(request: Request) {
  const { subscription, title, body, url } = await request.json();

  try {
    await webpush.sendNotification(
      subscription,
      JSON.stringify({ title, body, url })
    );
    return NextResponse.json({ success: true });
  } catch (error) {
    console.error('Push failed:', error);
    return NextResponse.json({ error: 'Push failed' }, { status: 500 });
  }
}
```
</step>

<step name="4. Store Subscriptions">
```typescript
// app/api/push/subscribe/route.ts
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
  const subscription = await request.json();

  // Store in your database
  // await db.pushSubscriptions.create({ data: subscription });

  return NextResponse.json({ success: true });
}
```
</step>
</implementation>

<ios_specific>
## iOS-Specific Considerations

<requirement name="Home Screen Installation">
Push notifications ONLY work on iOS when:
1. User visits site in Safari
2. User taps Share → "Add to Home Screen"
3. User opens PWA from home screen
4. User grants notification permission

**UX Recommendation:** Guide users through installation before asking for push permission.
</requirement>

<requirement name="Permission Flow">
```tsx
// Check if installed as PWA on iOS
const isIOSPWA = () => {
  return (
    'standalone' in navigator &&
    (navigator as any).standalone === true
  );
};

// Show appropriate UI
{!isIOSPWA() && isIOS() && (
  <div className="install-prompt">
    <p>Install this app to enable notifications</p>
    <p>Tap Share → Add to Home Screen</p>
  </div>
)}

{isIOSPWA() && permission === 'default' && (
  <button onClick={requestPermission}>
    Enable Notifications
  </button>
)}
```
</requirement>

<limitation name="No Background Delivery">
On iOS, push notifications may not be delivered if the PWA is completely closed. They work when:
- App is in foreground
- App is in background (recently used)
- Device receives push while app is "warm"

**Workaround:** Send push notifications for time-sensitive content. Don't rely on them for critical alerts on iOS.
</limitation>
</ios_specific>

<best_practices>
## Best Practices

1. **Ask at the right time** - Don't request permission on page load. Wait for user action or context that makes notifications relevant.

2. **Explain value first** - Tell users why they should enable notifications before asking.

3. **Handle denial gracefully** - If permission denied, show alternative (email notifications, in-app messages).

4. **Test on real devices** - Push behavior differs significantly between platforms.

5. **Keep payloads small** - Push payload limit varies (4KB typical). Send minimal data, fetch details on click.

6. **Include action buttons** - Make notifications actionable when appropriate.

7. **Respect quiet hours** - Consider time zones and don't send notifications at inappropriate times.

8. **Provide unsubscribe option** - Let users easily disable notifications in your app.
</best_practices>

<debugging>
## Debugging Push Notifications

**Chrome DevTools:**
1. Application tab → Service Workers
2. Click "Push" to simulate a push message
3. Check Console for errors

**Test payload:**
```json
{
  "title": "Test Notification",
  "body": "This is a test message",
  "url": "/notifications"
}
```

**Check subscription:**
```javascript
navigator.serviceWorker.ready.then(reg => {
  reg.pushManager.getSubscription().then(sub => {
    console.log('Subscription:', JSON.stringify(sub));
  });
});
```
</debugging>
