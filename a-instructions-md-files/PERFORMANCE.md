### 8. Performance Optimization Guide

# Performance Optimization Standards

## Performance Metrics (Gold Standards)

### Core Web Vitals
| Metric | Definition | Good | Target |
|--------|------------|------|--------|
| LCP | Largest Contentful Paint | ≤ 2.5s | < 1.8s |
| FID | First Input Delay | ≤ 100ms | < 50ms |
| CLS | Cumulative Layout Shift | ≤ 0.1 | < 0.05 |
| TTFB | Time to First Byte | ≤ 200ms | < 100ms |
| FCP | First Contentful Paint | ≤ 1.8s | < 1.0s |
| TTI | Time to Interactive | ≤ 3.8s | < 2.5s |
| SI | Speed Index | ≤ 3.4s | < 2.0s |

### Lighthouse Score Targets
- **Performance**: ≥ 90
- **Accessibility**: ≥ 95
- **Best Practices**: ≥ 90
- **SEO**: ≥ 95

## Frontend Performance

### Image Optimization
```html
<!-- Good image optimization -->
<picture>
  <source srcset="image.webp" type="image/webp">
  <source srcset="image.jpg" type="image/jpeg">
  <img src="image.jpg" alt="Description" loading="lazy" width="800" height="600">
</picture>

<!-- With srcset for responsive -->
<img
  src="image-800.jpg"
  srcset="image-400.jpg 400w, image-800.jpg 800w, image-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px, 800px"
  alt="Description"
  loading="lazy"
>
JavaScript Optimization
javascript
// ✅ Good: Code splitting
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Loading />,
  ssr: false
});

// ✅ Good: Lazy loading routes (React)
const routes = [
  {
    path: '/dashboard',
    component: lazy(() => import('./pages/Dashboard'))
  }
];

// ✅ Good: Debouncing/Rate limiting
const debouncedSearch = debounce((query) => {
  fetchResults(query);
}, 300);

// ❌ Bad: Blocking main thread
for (let i = 0; i < 1000000; i++) {
  heavyOperation(i);
}

// ✅ Good: Offload heavy work
setTimeout(() => {
  for (let i = 0; i < 1000000; i++) {
    heavyOperation(i);
  }
}, 0);
CSS Optimization
css
/* ✅ Good: Critical CSS inline */
<style>
  /* Critical styles for above-the-fold content */
  .header { /* styles */ }
  .hero { /* styles */ }
</style>

/* ✅ Good: Lazy load non-critical CSS */
<link
  rel="stylesheet"
  href="non-critical.css"
  media="print"
  onload="this.media='all'"
>

/* ✅ Good: Use CSS variables for theming */
:root {
  --color-primary: #0070f3;
  --spacing-base: 8px;
}

/* ✅ Good: Efficient selectors */
.nav-item { /* good */ }
ul li.nav-item { /* specific but okay */ }
div > ul > li.nav-item { /* too specific */ }
Backend Performance
API Optimization
javascript
// ✅ Good: Pagination
GET /api/users?page=1&limit=20

// ✅ Good: Filtering
GET /api/posts?category=tech&author=123

// ✅ Good: Caching headers
response.headers.set('Cache-Control', 'public, max-age=3600');

// ✅ Good: Compression
app.use(compression());

// ✅ Good: Database indexing
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);

// ❌ Bad: N+1 queries
const users = await User.find();
for (const user of users) {
  const posts = await Post.find({ userId: user.id });
}

// ✅ Good: Batch queries with joins
const users = await User.find().populate('posts');
Database Optimization
sql
-- ✅ Good: Proper joins
SELECT u.*, o.*
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.active = true;

-- ✅ Good: Use EXPLAIN ANALYZE
EXPLAIN ANALYZE
SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '30 days';

-- ✅ Good: Partition large tables
CREATE TABLE orders (
  id SERIAL,
  created_at DATE,
  -- columns
) PARTITION BY RANGE (created_at);

-- ✅ Good: Connection pooling
const pool = new Pool({
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000
});
Caching Strategy
Cache Levels
Browser Cache: Static assets (1 year)

CDN Cache: Static assets, images (1 month)

API Cache: API responses (1-15 minutes)

Database Cache: Query results (5-15 minutes)

Memory Cache: In-memory (Redis) (5-30 minutes)

Cache Implementation
javascript
// ✅ Good: Redis caching
const redis = require('redis');
const client = redis.createClient();

const getCachedData = async (key) => {
  const cached = await client.get(key);
  if (cached) return JSON.parse(cached);

  const data = await fetchFromDatabase();
  await client.set(key, JSON.stringify(data), 'EX', 300); // 5 min
  return data;
};

// ✅ Good: HTTP caching headers
res.setHeader('Cache-Control', 'public, max-age=3600');
res.setHeader('ETag', generateETag(data));
res.setHeader('Last-Modified', data.updatedAt.toUTCString());

// ✅ Good: Service Worker caching
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});
Network Optimization
Resource Optimization
html
<!-- ✅ Good: Preload critical resources -->
<link rel="preload" href="critical-font.woff2" as="font" crossorigin>
<link rel="preload" href="hero-image.jpg" as="image">

<!-- ✅ Good: Preconnect to origins -->
<link rel="preconnect" href="https://api.example.com">
<link rel="preconnect" href="https://fonts.googleapis.com">

<!-- ✅ Good: Prefetch for next page -->
<link rel="prefetch" href="/next-page">
HTTP/2 Optimization
Enable HTTP/2: Automatically multiplexes requests

Server Push: Push critical resources (use with caution)

Domain Sharding: Split resources across domains (optional)

CDN Implementation
javascript
// ✅ Good: CDN URLs for static assets
const assets = {
  images: 'https://cdn.example.com/images/',
  scripts: 'https://cdn.example.com/scripts/',
  styles: 'https://cdn.example.com/styles/'
};

// ✅ Good: CDN for API responses
const api = 'https://api-cdn.example.com/v1/';
Performance Monitoring
Tools & Setup
Lighthouse: Chrome DevTools

Web Vitals: npm i web-vitals

Real User Monitoring (RUM): Google Analytics

Synthetic Monitoring: PageSpeed Insights, GTmetrix

APM: New Relic, Datadog, Sentry

Implementation
javascript
// ✅ Good: Track web vitals
import { onCLS, onFID, onLCP } from 'web-vitals';

function sendToAnalytics(metric) {
  // Send to Google Analytics, Segment, etc.
  console.log('Web Vitals:', metric.name, metric.value);
}

onCLS(sendToAnalytics);
onFID(sendToAnalytics);
onLCP(sendToAnalytics);

// ✅ Good: Track API performance
console.time('api-request');
const response = await fetch('/api/data');
console.timeEnd('api-request');

// ✅ Good: Track page load times
window.addEventListener('load', () => {
  const perfData = performance.timing;
  const loadTime = perfData.loadEventEnd - perfData.navigationStart;
  // Send to analytics
});
Performance Budgets
Resource Budgets
Resource	Limit
JavaScript	≤ 200KB (gzipped)
CSS	≤ 50KB (gzipped)
Images	≤ 200KB (total above the fold)
Fonts	≤ 100KB (total)
Total Page	≤ 1MB (first load)
Time Budgets
Metric	Target
Time to Interactive	≤ 2.5s
First Contentful Paint	≤ 1.0s
Time to First Byte	≤ 100ms
API Response	≤ 200ms (P95)
Database Query	≤ 100ms (P95)
Optimization Checklist
Lighthouse score ≥ 90

Images optimized (WebP, lazy loading)

JavaScript minified and bundled

CSS minified and optimized

Critical CSS inlined

Fonts optimized (WOFF2, preload)

HTTP/2 enabled

CDN configured

Caching implemented (browser, CDN, API)

Database queries optimized

Indexes on all foreign keys

Connection pooling configured

Gzip/Brotli compression

Tree shaking enabled

Code splitting configured

Service worker installed

Performance monitoring set up

Performance budget defined

Bundle analyzer configured

Third-party scripts audited