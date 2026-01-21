# Building a Blazing Fast Website Like McMaster-Carr

McMaster-Carr has one of the fastest websites on the internet — and they're using 15-year-old technology (ASP.NET, jQuery, YUI). This guide breaks down how they do it and how you can replicate it with modern tools.

---

## Part 1: Why Speed Matters — The 14kB Rule

### The Physics of Page Load

When a browser first connects to your server, the server doesn't know how much bandwidth is available. So it starts conservatively with **TCP slow start**, sending just 10 TCP packets.

- Max TCP packet size: **1,500 bytes**
- Header overhead per packet: **40 bytes**
- Usable data per packet: **1,460 bytes**
- 10 packets × 1,460 bytes = **~14kB**

If your critical content fits in that first 14kB, users see something useful *before the server even confirms the packets arrived*.

### The Cliff Effect

| Page Size | Load Behavior |
|-----------|---------------|
| 14kB | Arrives in first round trip |
| 15kB | Requires second round trip |
| Difference | **612ms+ on slow connections** |

The jump from 15kB to 16kB? Trivial. But crossing the 14kB threshold costs you an entire round trip.

### Real-World Latency

| Connection Type | Latency Per Round Trip |
|-----------------|------------------------|
| 2G Mobile | 300–1000ms |
| 3G Mobile | 100–500ms |
| Satellite (planes, ships, oil rigs) | 612ms+ |
| Congested networks | Highly variable |

**Note:** The 14kB budget includes compression (gzip/brotli), so you actually get ~50kB of uncompressed HTML/CSS to work with.

---

## Part 2: What McMaster-Carr Does

### Server & Rendering

| Technique | What It Does |
|-----------|--------------|
| **Server-rendered HTML** | No JS framework. Server returns HTML, browser renders immediately. |
| **Prefetch on hover** | When you hover a link, it pre-downloads that page's HTML. |
| **Partial page swaps** | Only replaces content that changes (not nav, cart). Uses `pushState` for clean URLs. |

### Caching (Three Layers Deep)

| Layer | Technology | Benefit |
|-------|------------|---------|
| **CDN** | Akamai | Pre-rendered HTML cached globally |
| **Proxy** | Squid Cache | Additional caching layer |
| **Browser** | Service Worker | Repeat visits load in ~7ms instead of ~65ms |

### Resource Loading

| Technique | Implementation |
|-----------|----------------|
| **Link preloads** | Tells browser to fetch fonts/resources before it knows it needs them |
| **DNS prefetching** | Pre-resolves domain names for external resources |
| **Critical CSS inlined** | CSS in a `<style>` tag in `<head>`, not external files |
| **Page-specific JS** | Only loads JavaScript needed for that specific page |

### Images

| Technique | Benefit |
|-----------|---------|
| **Explicit width/height** | Prevents layout shift as images load |
| **CSS sprites** | All page images in one 93kB file (fewer requests) |

### Monitoring

They use `window.performance` and `performance.mark()` everywhere — measuring everything obsessively.

---

## Part 3: Modern Implementation

### Choose Your Stack

#### Option A: Modern Framework (Easier)

**Remix** or **Astro** give you most of this out of the box:
- Server-rendered HTML by default
- Built-in prefetching on hover
- Automatic code-splitting
- Easy edge deployment

#### Option B: Lightweight/No Framework (More Control)

**HTMX + any backend** (Go, Python, Rails, PHP):
- Returns HTML fragments like McMaster-Carr
- Swap only changed parts of the page
- ~14kB total JS footprint
- Full control over everything

### Recommended Modern Stack

```
Astro or Remix      → Server rendering, code-splitting
Cloudflare Pages    → Global CDN, edge caching  
Workbox             → Service worker made easy
Vite                → Fast builds, automatic optimization
```

---

## Part 4: Implementation Details

### 1. Inline Critical CSS

Put only what's needed for above-the-fold content directly in the HTML:

```html
<head>
  <style>
    /* Critical styles only */
    body { font-family: system-ui, sans-serif; margin: 0; }
    .header { /* ... */ }
    .hero { /* ... */ }
  </style>
  <!-- Non-critical CSS loads async -->
  <link rel="preload" href="/styles/full.css" as="style" onload="this.rel='stylesheet'">
</head>
```

**Build tools that extract critical CSS automatically:**
- Critters (works with Vite/Webpack)
- Critical (by Addy Osmani)
- Astro (built-in)

### 2. Prefetch on Hover

If your framework doesn't do this automatically:

```javascript
document.querySelectorAll('a').forEach(link => {
  link.addEventListener('mouseenter', () => {
    const prefetch = document.createElement('link')
    prefetch.rel = 'prefetch'
    prefetch.href = link.href
    document.head.appendChild(prefetch)
  }, { once: true })
})
```

Or use the modern **Speculation Rules API**:

```html
<script type="speculationrules">
{
  "prefetch": [
    { "where": { "selector_matches": "a[href^='/']" } }
  ]
}
</script>
```

### 3. Service Worker with Workbox

```javascript
// sw.js
import { precacheAndRoute } from 'workbox-precaching'
import { registerRoute } from 'workbox-routing'
import { StaleWhileRevalidate, CacheFirst } from 'workbox-strategies'
import { ExpirationPlugin } from 'workbox-expiration'

// Precache build assets
precacheAndRoute(self.__WB_MANIFEST)

// Cache pages with stale-while-revalidate
registerRoute(
  ({ request }) => request.mode === 'navigate',
  new StaleWhileRevalidate({
    cacheName: 'pages',
  })
)

// Cache images with cache-first
registerRoute(
  ({ request }) => request.destination === 'image',
  new CacheFirst({
    cacheName: 'images',
    plugins: [
      new ExpirationPlugin({
        maxEntries: 100,
        maxAgeSeconds: 30 * 24 * 60 * 60, // 30 days
      }),
    ],
  })
)
```

Register it:

```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js')
}
```

### 4. Preload Critical Resources

```html
<head>
  <!-- Preload fonts (highest priority) -->
  <link rel="preload" href="/fonts/main.woff2" as="font" type="font/woff2" crossorigin>
  
  <!-- Preload hero image -->
  <link rel="preload" href="/images/hero.webp" as="image">
  
  <!-- DNS prefetch for external domains -->
  <link rel="dns-prefetch" href="//cdn.example.com">
  <link rel="dns-prefetch" href="//images.example.com">
  
  <!-- Preconnect for critical external resources -->
  <link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>
</head>
```

### 5. Always Set Image Dimensions

Prevents Cumulative Layout Shift (CLS):

```html
<!-- Always include width and height -->
<img 
  src="/product.webp" 
  width="400" 
  height="300" 
  alt="Product description"
  loading="lazy"
>
```

For responsive images:

```html
<img
  src="/product.webp"
  width="400"
  height="300"
  sizes="(max-width: 600px) 100vw, 400px"
  srcset="
    /product-200.webp 200w,
    /product-400.webp 400w,
    /product-800.webp 800w
  "
  alt="Product description"
  loading="lazy"
>
```

### 6. Partial Page Updates (HTMX Example)

```html
<!-- Navigation stays, content swaps -->
<nav><!-- persistent --></nav>

<main id="content" hx-boost="true">
  <!-- This gets swapped on navigation -->
</main>

<script src="https://unpkg.com/htmx.org@1.9.10"></script>
```

Or with the **View Transitions API**:

```javascript
document.querySelectorAll('a').forEach(link => {
  link.addEventListener('click', async (e) => {
    e.preventDefault()
    
    const response = await fetch(link.href)
    const html = await response.text()
    const parser = new DOMParser()
    const doc = parser.parseFromString(html, 'text/html')
    
    document.startViewTransition(() => {
      document.querySelector('main').innerHTML = 
        doc.querySelector('main').innerHTML
      history.pushState({}, '', link.href)
    })
  })
})
```

---

## Part 5: Measuring Success

### Check Your 14kB Budget

```bash
# Compressed size of your page
curl -so /dev/null -w '%{size_download}' --compressed https://yoursite.com

# Detailed breakdown
curl -w "Size: %{size_download} bytes\nTime: %{time_total}s\n" -so /dev/null https://yoursite.com
```

### Core Web Vitals Targets

| Metric | Target | What It Measures |
|--------|--------|------------------|
| **LCP** (Largest Contentful Paint) | < 2.5s | When main content is visible |
| **FID** (First Input Delay) | < 100ms | Responsiveness to interaction |
| **CLS** (Cumulative Layout Shift) | < 0.1 | Visual stability |

### Tools

- **Lighthouse** (Chrome DevTools)
- **WebPageTest.org** (real device testing)
- **Chrome DevTools Performance tab**
- **`window.performance` API** for custom metrics

---

## Part 6: What to Avoid

### The Junk That Slows You Down

McMaster-Carr is fast partly because of what they *don't* have:

- ❌ Cookie consent banners (they don't track you)
- ❌ Social media buttons
- ❌ Third-party analytics scripts
- ❌ Chat widgets
- ❌ Autoplay videos
- ❌ Popups and modals
- ❌ Heavy JS frameworks for simple pages

### Modern Anti-Patterns

- ❌ Client-side rendering for content that doesn't need it
- ❌ Loading all JS upfront instead of per-page
- ❌ External CSS files for critical styles
- ❌ Images without dimensions
- ❌ Unoptimized web fonts (use `font-display: swap`)

---

## Part 7: Quick Wins Checklist

```
□ Server-render your HTML
□ Inline critical CSS in <head>
□ Set width/height on all images
□ Use modern image formats (WebP/AVIF)
□ Preload fonts and critical resources
□ Add DNS prefetch for external domains
□ Implement prefetching on hover
□ Set up a service worker for caching
□ Deploy to edge CDN (Cloudflare, Vercel, etc.)
□ Only load JS needed for each page
□ Measure everything with performance marks
□ Check that critical content fits in 14kB compressed
```

---

## The Bottom Line

McMaster-Carr proves that **speed is a choice, not a technology**.

They're running 15-year-old tech (ASP.NET, jQuery, YUI from 2008) and beating most modern websites. The difference isn't the framework — it's the discipline:

1. **Treat every kilobyte as a cost**
2. **Make HTML useful immediately** (not a spinner waiting for JS)
3. **Don't load what you don't need**
4. **Cache aggressively at every layer**
5. **Measure obsessively**

The tech stack matters far less than the commitment to speed.

---

## Resources

- [High Performance Browser Networking](https://hpbn.co/) — Ilya Grigorik
- [Why your website should be under 14kB](https://endtimes.dev/why-your-website-should-be-under-14kb-in-size/)
- [web.dev Performance](https://web.dev/performance/)
- [Workbox Documentation](https://developer.chrome.com/docs/workbox/)
- [View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)
- [Speculation Rules API](https://developer.chrome.com/docs/web-platform/prerender-pages)
