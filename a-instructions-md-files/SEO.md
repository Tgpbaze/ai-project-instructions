### 7. SEO Optimization Guidelines
```markdown
# SEO.md - SEO Optimization Guidelines

## On-Page SEO

### Meta Tags
```html
<!-- Required Meta Tags -->
<title>Page Title - Site Name | Keywords</title>
<meta name="description" content="Concise description (150-160 characters)">
<meta name="keywords" content="keyword1, keyword2, keyword3">
<meta name="robots" content="index, follow">

<!-- Open Graph (Social) -->
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Description">
<meta property="og:image" content="https://example.com/og-image.jpg">
<meta property="og:url" content="https://example.com/page">
<meta property="og:type" content="website">
<meta property="og:site_name" content="Site Name">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Page Title">
<meta name="twitter:description" content="Description">
<meta name="twitter:image" content="https://example.com/twitter-image.jpg">

URL Structure
javascript
// Good URLs
/users/123/profile
/products/category/product-name
/blog/understanding-seo

// Bad URLs
/index.php?page=123
/123?cat=5
/unsafe-url-with-special-chars
Header Hierarchy
html
<!-- Good hierarchy -->
<h1>Main Topic</h1>
  <h2>Subtopic 1</h2>
    <h3>Details</h3>
  <h2>Subtopic 2</h2>
    <h3>Details</h3>

<!-- Bad (skipping levels) -->
<h1>Main Topic</h1>
  <h3>Subtopic 1</h3>
  <h4>Details</h4>
Content Optimization
Keyword Strategy
Primary Keywords: 1-3 main keywords per page

Secondary Keywords: Related/semantic keywords

LSI Keywords: Latent Semantic Indexing (related terms)

Keyword Density: 1-2% natural usage

Long-tail Keywords: 3-4 word phrases (easier to rank)

Content Quality
Length: 1000+ words for ranking pages

Readability: 8th-9th grade reading level

Structure: Use bullet points, lists, subheadings

Value: Solve user problems, answer questions

Originality: Unique content, not duplicate

Freshness: Update content regularly

Internal Linking
html
<!-- Good internal linking -->
<a href="/category/related">Related Category</a>
<a href="/blog/learn-more">Read More About Topic</a>

<!-- Bad internal linking -->
<a href="/details">Click Here</a>
<a href="/page">Learn More</a>
Technical SEO
XML Sitemap
xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2024-01-01</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <!-- More URLs -->
</urlset>
Robots.txt
text
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /private/
Sitemap: https://example.com/sitemap.xml
Structured Data (Schema.org)
html
<!-- JSON-LD Schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Article Title",
  "description": "Description",
  "image": "https://example.com/image.jpg",
  "author": {
    "@type": "Person",
    "name": "Author Name"
  },
  "datePublished": "2024-01-01",
  "publisher": {
    "@type": "Organization",
    "name": "Site Name",
    "logo": "https://example.com/logo.jpg"
  }
}
</script>
Mobile SEO
Responsive Design: Mobile-first approach

Speed: Optimized for mobile networks

UX: Mobile-friendly navigation

Accelerated Mobile Pages (AMP): Consider for news/blog

Touch Targets: Minimum 44px for tap actions

Speed Optimization
Core Web Vitals
Metric	Good	Needs Improvement	Poor
LCP	≤ 2.5s	≤ 4.0s	> 4.0s
FID	≤ 100ms	≤ 300ms	> 300ms
CLS	≤ 0.1	≤ 0.25	> 0.25
Performance Tips
Image Optimization: WebP, compression, lazy loading

Minification: CSS, JS, HTML

Caching: Browser cache, CDN

Critical CSS: Inline above-the-fold CSS

Async/Defer: Load scripts asynchronously

Font Optimization: Subset, preload, WOFF2

Off-Page SEO
Backlink Strategy
Quality Over Quantity: High authority sites

Natural Links: Earned through good content

Diverse Sources: Different domains and IPs

Anchor Text: Varied, natural text

Follow vs Nofollow: Mix of both

Social Signals
Active Presence: Regular posting

Engagement: Respond to comments

Sharing: Easy social share buttons

Visual Content: Images, videos, infographics

Monitoring & Analytics
Key Metrics
Metric	What It Measures	Target
Organic Traffic	Visitors from search engines	Increasing
Bounce Rate	Single-page visits	< 50%
Pages/Session	Engagement	> 3 pages
CTR	Click-through rate	> 2%
Conversion Rate	Goal completion	Varies
Keyword Rankings	Position for keywords	Top 10
Tools
Google Analytics: Traffic tracking

Google Search Console: Performance monitoring

SEMrush/Ahrefs: Competitive analysis

Screaming Frog: Technical SEO audit

GTmetrix: Speed testing

Local SEO (For Physical Businesses)
Google My Business
Claim listing: Verify business

Complete profile: Hours, phone, address

Photos: Exterior, interior, products

Reviews: Respond to all reviews

Posts: Regular updates, offers

Local Schema
html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Business Name",
  "address": "123 Main St, City, State",
  "telephone": "+1-555-555-5555",
  "openingHours": "Mo-Fr 09:00-17:00"
}
</script>
SEO Audit Checklist
Meta titles and descriptions (all pages)

Proper heading hierarchy (H1, H2, H3)

Optimized images (alt text, file names)

Internal linking structure

XML sitemap and robots.txt

Structured data implemented

Mobile responsive design

Fast page load speed

Clean URL structure

Social media integration

Google Analytics setup

Search Console setup

SSL/HTTPS enabled

Broken links fixed

Duplicate content addressed