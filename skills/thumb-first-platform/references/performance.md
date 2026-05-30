<overview>
Mobile web performance directly impacts user experience and SEO. Google's Core Web Vitals measure real-world user experience through LCP, INP, and CLS. Mobile devices have slower CPUs, less memory, and often slower networks than desktop, making optimization critical.
</overview>

<core_web_vitals>
## Core Web Vitals (2024-2025)

<metric name="LCP - Largest Contentful Paint">
**What:** Time until the largest visible content element renders.

**Target:** < 2.5 seconds

**Common LCP elements:**
- Hero images (73% of mobile pages)
- Hero text/headings
- Video poster images

**How to improve:**
- Preload LCP image with `priority` prop (Next.js)
- Use appropriate image format (WebP/AVIF)
- Optimize server response time
- Remove render-blocking resources
- Use CDN for static assets
</metric>

<metric name="INP - Interaction to Next Paint">
**What:** Measures responsiveness - time from user interaction to visual feedback.

**Target:** < 200 milliseconds

**Note:** INP replaced FID (First Input Delay) in March 2024.

**INP components:**
1. Input delay (event queue wait time)
2. Processing delay (event handler execution)
3. Presentation delay (rendering/painting)

**How to improve:**
- Break up long tasks (> 50ms)
- Use `requestIdleCallback` for non-critical work
- Defer/lazy-load non-essential JavaScript
- Use React transitions for heavy updates
- Avoid layout thrashing
</metric>

<metric name="CLS - Cumulative Layout Shift">
**What:** Measures visual stability - unexpected layout shifts.

**Target:** < 0.1

**Common causes:**
- Images without dimensions
- Ads/embeds without reserved space
- Web fonts causing FOUT/FOIT
- Dynamic content insertion

**How to improve:**
- Always specify width/height on images
- Reserve space for dynamic content
- Use `font-display: swap` for fonts
- Avoid inserting content above existing content
- Use CSS transforms instead of layout properties
</metric>
</core_web_vitals>

<measuring>
## Measuring Performance

<tool name="Lighthouse">
```
DevTools → Lighthouse → Mobile → Generate Report
```

**Settings for realistic mobile:**
- Device: Mobile
- Categories: Performance
- Throttling: Simulated throttling (default)

**Note:** Lighthouse simulates mobile, but real device testing is more accurate.
</tool>

<tool name="Chrome User Experience Report (CrUX)">
Real-world data from Chrome users. Access via:
- PageSpeed Insights (includes CrUX data)
- Search Console → Core Web Vitals report
- BigQuery for raw data
</tool>

<tool name="web-vitals library">
```tsx
// app/layout.tsx or client component
'use client';

import { useReportWebVitals } from 'next/web-vitals';

export function WebVitals() {
  useReportWebVitals((metric) => {
    // Log or send to analytics
    console.log(metric.name, metric.value);

    // Send to analytics
    if (metric.name === 'LCP' || metric.name === 'INP' || metric.name === 'CLS') {
      sendToAnalytics(metric);
    }
  });

  return null;
}
```
</tool>

<tool name="DevTools Performance Panel">
Record real interactions:
1. DevTools → Performance
2. Click Record
3. Interact with page
4. Stop recording
5. Analyze flame chart for long tasks
</tool>
</measuring>

<lcp_optimization>
## LCP Optimization

<technique name="Next.js Image Priority">
```tsx
import Image from 'next/image';

// For hero/above-fold images
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // Disables lazy loading, adds preload
/>
```
</technique>

<technique name="Preload Critical Resources">
```tsx
// In layout.tsx metadata
export const metadata = {
  other: {
    link: [
      { rel: 'preload', href: '/critical-image.webp', as: 'image' },
      { rel: 'preload', href: '/fonts/custom.woff2', as: 'font', crossOrigin: 'anonymous' },
    ],
  },
};
```
</technique>

<technique name="Optimize Images">
```javascript
// next.config.js
module.exports = {
  images: {
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
};
```
</technique>

<technique name="Reduce Server Response Time">
- Use edge functions / CDN
- Cache database queries
- Optimize API endpoints
- Use streaming SSR when appropriate
</technique>
</lcp_optimization>

<inp_optimization>
## INP Optimization

<technique name="Break Up Long Tasks">
```tsx
// Bad: Long synchronous task
function processData(items) {
  items.forEach(item => heavyOperation(item));
}

// Good: Yield to main thread
async function processData(items) {
  for (const item of items) {
    heavyOperation(item);
    // Yield to allow other work
    await new Promise(resolve => setTimeout(resolve, 0));
  }
}
```
</technique>

<technique name="Use React Transitions">
```tsx
import { useTransition } from 'react';

function SearchResults() {
  const [isPending, startTransition] = useTransition();
  const [results, setResults] = useState([]);

  function handleSearch(query: string) {
    // Non-urgent update
    startTransition(() => {
      setResults(expensiveSearch(query));
    });
  }

  return (
    <>
      <input onChange={(e) => handleSearch(e.target.value)} />
      {isPending && <Spinner />}
      <Results data={results} />
    </>
  );
}
```
</technique>

<technique name="Lazy Load Components">
```tsx
import dynamic from 'next/dynamic';

// Load heavy component only when needed
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,
});
```
</technique>

<technique name="Debounce/Throttle Event Handlers">
```tsx
import { useDebouncedCallback } from 'use-debounce';

function SearchInput() {
  const debouncedSearch = useDebouncedCallback((value) => {
    // Heavy search operation
    performSearch(value);
  }, 300);

  return <input onChange={(e) => debouncedSearch(e.target.value)} />;
}
```
</technique>
</inp_optimization>

<cls_optimization>
## CLS Optimization

<technique name="Always Specify Image Dimensions">
```tsx
// Always include width and height
<Image
  src="/photo.jpg"
  width={400}
  height={300}
  alt="Photo"
/>
```
</technique>

<technique name="Reserve Space for Dynamic Content">
```tsx
// Skeleton with same dimensions as final content
{isLoading ? (
  <div className="h-48 w-full animate-pulse bg-muted rounded" />
) : (
  <ContentCard />
)}
```
</technique>

<technique name="Use CSS Transforms for Animations">
```css
/* Bad - causes layout shift */
.animate {
  animation: slide 0.3s;
}
@keyframes slide {
  from { margin-left: -100px; }
  to { margin-left: 0; }
}

/* Good - GPU accelerated, no layout shift */
.animate {
  animation: slide 0.3s;
}
@keyframes slide {
  from { transform: translateX(-100px); }
  to { transform: translateX(0); }
}
```
</technique>

<technique name="Handle Fonts Properly">
```tsx
// Next.js font optimization
import { Inter } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],
  display: 'swap', // Prevents FOIT
  preload: true,
});
```
</technique>

<technique name="Reserve Space for Ads/Embeds">
```tsx
<div className="min-h-[250px]"> {/* Standard ad height */}
  <AdComponent />
</div>
```
</technique>
</cls_optimization>

<mobile_specific>
## Mobile-Specific Optimizations

<optimization name="Reduce JavaScript">
Mobile CPUs are 4-5x slower than desktop. Minimize and defer JavaScript.

```tsx
// Defer non-critical scripts
<Script src="/analytics.js" strategy="lazyOnload" />
<Script src="/chat-widget.js" strategy="lazyOnload" />
```
</optimization>

<optimization name="Use Efficient Image Formats">
- WebP: 25-34% smaller than JPEG
- AVIF: 50% smaller than JPEG (but slower to encode)

Next.js Image component handles this automatically.
</optimization>

<optimization name="Minimize Network Requests">
- Bundle CSS/JS appropriately
- Use HTTP/2 or HTTP/3
- Enable compression (gzip/brotli)
- Cache aggressively
</optimization>

<optimization name="Test on Real Devices">
Chrome DevTools throttling doesn't match real mobile performance.

Test on:
- Mid-range Android phone
- iPhone SE (not just latest iPhone)
- Slow 3G network conditions
</optimization>
</mobile_specific>

<bundle_analysis>
## Bundle Analysis

```bash
# Add analyzer
pnpm add -D @next/bundle-analyzer

# Update next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});
module.exports = withBundleAnalyzer(nextConfig);

# Run analysis
ANALYZE=true pnpm build
```

Look for:
- Large dependencies that could be replaced
- Duplicate packages
- Unused exports
- Heavy polyfills
</bundle_analysis>
