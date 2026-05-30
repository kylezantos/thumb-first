# Workflow: Optimize Mobile Performance

<required_reading>
**Read these reference files before optimizing:**
1. references/performance.md
2. references/image-optimization.md
</required_reading>

<process>
## Step 1: Measure Current Performance

Run Lighthouse audit:
```
DevTools → Lighthouse → Mobile → Performance
```

Record baseline metrics:
- **LCP** (Largest Contentful Paint): Should be < 2.5s
- **INP** (Interaction to Next Paint): Should be < 200ms
- **CLS** (Cumulative Layout Shift): Should be < 0.1

Also check:
- Total blocking time
- First contentful paint
- Speed index

## Step 2: Optimize Largest Contentful Paint (LCP)

**73% of mobile pages have an image as LCP element.**

### Identify LCP Element
```
DevTools → Performance → Record page load → Look for "LCP" marker
```

### Image LCP Optimization

**Use Next.js Image component:**
```tsx
import Image from 'next/image'

// For LCP images, add priority prop
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // Disables lazy loading, preloads image
  sizes="100vw" // Tell browser expected size
/>
```

**Enable modern formats in next.config.js:**
```javascript
module.exports = {
  images: {
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
}
```

**Preload critical images:**
```tsx
// In layout.tsx or page.tsx metadata
export const metadata = {
  other: {
    'link': [
      { rel: 'preload', as: 'image', href: '/hero.webp' }
    ]
  }
}
```

### Text LCP Optimization

If LCP is text, optimize fonts:
```tsx
// app/layout.tsx
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap', // Prevent FOIT
  preload: true,
})
```

## Step 3: Optimize Interaction to Next Paint (INP)

**INP replaced FID in March 2024.**

### Identify Slow Interactions
```
DevTools → Performance → Record interactions → Look for long tasks
```

### Reduce JavaScript Execution

**Code split with dynamic imports:**
```tsx
import dynamic from 'next/dynamic'

// Lazy load heavy components
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,
})
```

**Defer non-critical scripts:**
```tsx
<Script
  src="https://analytics.example.com"
  strategy="lazyOnload"
/>
```

**Use React transitions for heavy updates:**
```tsx
import { useTransition } from 'react'

function SearchResults() {
  const [isPending, startTransition] = useTransition()

  function handleSearch(query: string) {
    startTransition(() => {
      setSearchResults(expensiveSearch(query))
    })
  }
}
```

### Optimize Event Handlers

**Debounce scroll/resize handlers:**
```tsx
import { useDebouncedCallback } from 'use-debounce'

const handleScroll = useDebouncedCallback(() => {
  // Heavy operation
}, 100)
```

## Step 4: Optimize Cumulative Layout Shift (CLS)

### Reserve Space for Dynamic Content

**Images - always specify dimensions:**
```tsx
<Image
  src="/photo.jpg"
  width={400}
  height={300}
  alt="Photo"
/>
```

**Skeleton loaders for async content:**
```tsx
{isLoading ? (
  <div className="h-48 w-full animate-pulse bg-muted rounded" />
) : (
  <ActualContent />
)}
```

### Avoid Layout-Triggering Animations

**Bad - causes reflow:**
```css
.animate {
  animation: slide 0.3s;
}
@keyframes slide {
  from { margin-left: -100px; }
  to { margin-left: 0; }
}
```

**Good - GPU accelerated:**
```css
.animate {
  animation: slide 0.3s;
}
@keyframes slide {
  from { transform: translateX(-100px); }
  to { transform: translateX(0); }
}
```

### Handle Dynamic Ads/Embeds

Reserve fixed space:
```tsx
<div className="min-h-[250px]"> {/* Standard ad height */}
  <AdComponent />
</div>
```

## Step 5: Optimize for Mobile Networks

### Enable Compression

In `next.config.js`:
```javascript
module.exports = {
  compress: true,
}
```

### Minimize Bundle Size

Analyze bundle:
```bash
pnpm add -D @next/bundle-analyzer
ANALYZE=true pnpm build
```

Remove unused dependencies:
```bash
npx depcheck
```

### Use CDN for Static Assets

Configure in `next.config.js`:
```javascript
module.exports = {
  assetPrefix: 'https://cdn.example.com',
}
```

## Step 6: Implement Performance Monitoring

**Use web-vitals library:**
```tsx
// app/layout.tsx or a client component
'use client'

import { useReportWebVitals } from 'next/web-vitals'

export function WebVitals() {
  useReportWebVitals((metric) => {
    console.log(metric) // or send to analytics
  })
  return null
}
```

## Step 7: Test on Real Mobile Hardware

Chrome DevTools throttling doesn't match real devices:

1. Test on mid-range Android device (not just flagship)
2. Test on 3G connection
3. Use Chrome's "Slow 3G" preset as minimum bar
4. Check Lighthouse in "Applied Slow 4G" mode
</process>

<anti_patterns>
**Avoid:**
- Inlining large images as base64 (blocks rendering)
- Not specifying image dimensions (causes CLS)
- Loading all components eagerly (hurts INP)
- Using layout-triggering CSS properties for animations
- Testing only on fast devices/connections
- Ignoring INP (new metric many overlook)
</anti_patterns>

<success_criteria>
Performance is optimized when:
- [ ] LCP < 2.5 seconds on mobile
- [ ] INP < 200ms
- [ ] CLS < 0.1
- [ ] Lighthouse Performance score > 90
- [ ] Images use next/image with proper sizes
- [ ] Heavy components are code-split
- [ ] No render-blocking resources
- [ ] Tested on real mobile device with slow connection
</success_criteria>
