# Document Structure and Meta Information

## Introduction
Proper document structure and meta information are essential for SEO, accessibility, and browser compatibility. This lesson covers HTML document structure, meta tags, and semantic organization.

## HTML Document Structure

### Basic Document Template
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Character encoding -->
    <meta charset="UTF-8">
    
    <!-- Viewport for responsive design -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- Page title -->
    <title>Page Title - Site Name</title>
    
    <!-- Meta description for SEO -->
    <meta name="description" content="Brief description of the page content">
    
    <!-- Meta keywords (less important now) -->
    <meta name="keywords" content="keyword1, keyword2, keyword3">
    
    <!-- Author information -->
    <meta name="author" content="Your Name">
    
    <!-- Robots meta tag -->
    <meta name="robots" content="index, follow">
    
    <!-- Canonical URL -->
    <link rel="canonical" href="https://example.com/page">
    
    <!-- Favicon -->
    <link rel="icon" type="image/x-icon" href="/favicon.ico">
    <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
    
    <!-- Stylesheets -->
    <link rel="stylesheet" href="styles.css">
    
    <!-- Preconnect to external domains -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    
    <!-- Font loading -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
</head>
<body>
    <!-- Skip link for accessibility -->
    <a href="#main-content" class="skip-link">Skip to main content</a>
    
    <!-- Header -->
    <header role="banner">
        <nav role="navigation" aria-label="Main navigation">
            <!-- Navigation content -->
        </nav>
    </header>
    
    <!-- Main content -->
    <main id="main-content" role="main">
        <!-- Main content goes here -->
    </main>
    
    <!-- Footer -->
    <footer role="contentinfo">
        <!-- Footer content -->
    </footer>
    
    <!-- Scripts -->
    <script src="script.js"></script>
</body>
</html>
```

### Advanced Meta Tags
```html
<head>
    <!-- Open Graph meta tags for social media -->
    <meta property="og:title" content="Page Title">
    <meta property="og:description" content="Page description">
    <meta property="og:image" content="https://example.com/image.jpg">
    <meta property="og:url" content="https://example.com/page">
    <meta property="og:type" content="website">
    <meta property="og:site_name" content="Site Name">
    
    <!-- Twitter Card meta tags -->
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Page Title">
    <meta name="twitter:description" content="Page description">
    <meta name="twitter:image" content="https://example.com/image.jpg">
    <meta name="twitter:site" content="@yourusername">
    
    <!-- Theme color for mobile browsers -->
    <meta name="theme-color" content="#ffffff">
    <meta name="msapplication-TileColor" content="#ffffff">
    
    <!-- Mobile app configuration -->
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="default">
    <meta name="apple-mobile-web-app-title" content="App Name">
    
    <!-- Security headers -->
    <meta http-equiv="X-Content-Type-Options" content="nosniff">
    <meta http-equiv="X-Frame-Options" content="DENY">
    <meta http-equiv="X-XSS-Protection" content="1; mode=block">
    
    <!-- Cache control -->
    <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
    <meta http-equiv="Pragma" content="no-cache">
    <meta http-equiv="Expires" content="0">
</head>
```

## Semantic Document Organization

### Proper Heading Hierarchy
```html
<main>
    <article>
        <header>
            <h1>Main Article Title</h1>
            <p class="article-meta">
                By <span class="author">John Doe</span> on 
                <time datetime="2024-01-15">January 15, 2024</time>
            </p>
        </header>
        
        <section>
            <h2>Introduction</h2>
            <p>Introduction content...</p>
        </section>
        
        <section>
            <h2>Main Content</h2>
            <p>Main content...</p>
            
            <section>
                <h3>Subsection Title</h3>
                <p>Subsection content...</p>
                
                <section>
                    <h4>Sub-subsection Title</h4>
                    <p>Sub-subsection content...</p>
                </section>
            </section>
        </section>
        
        <section>
            <h2>Conclusion</h2>
            <p>Conclusion content...</p>
        </section>
        
        <footer>
            <h2>About the Author</h2>
            <p>Author information...</p>
        </footer>
    </article>
    
    <aside>
        <h2>Related Articles</h2>
        <nav aria-label="Related articles">
            <ul>
                <li><a href="#">Related Article 1</a></li>
                <li><a href="#">Related Article 2</a></li>
            </ul>
        </nav>
    </aside>
</main>
```

### Content Structure Patterns
```html
<!-- Blog post structure -->
<article class="blog-post">
    <header class="post-header">
        <h1 class="post-title">How to Build Accessible Web Applications</h1>
        <div class="post-meta">
            <span class="post-author">By <a href="/author/jane-doe">Jane Doe</a></span>
            <time class="post-date" datetime="2024-01-15">January 15, 2024</time>
            <span class="post-reading-time">5 min read</span>
        </div>
        <div class="post-tags">
            <span class="tag">Accessibility</span>
            <span class="tag">Web Development</span>
            <span class="tag">ARIA</span>
        </div>
    </header>
    
    <div class="post-content">
        <section>
            <h2>Introduction</h2>
            <p>Building accessible web applications is crucial...</p>
        </section>
        
        <section>
            <h2>Key Principles</h2>
            <p>There are several key principles to follow...</p>
        </section>
        
        <section>
            <h2>Implementation</h2>
            <p>Let's look at how to implement these principles...</p>
        </section>
    </div>
    
    <footer class="post-footer">
        <div class="post-share">
            <h3>Share this article</h3>
            <div class="share-buttons">
                <a href="#" aria-label="Share on Twitter">Twitter</a>
                <a href="#" aria-label="Share on LinkedIn">LinkedIn</a>
                <a href="#" aria-label="Share on Facebook">Facebook</a>
            </div>
        </div>
        
        <div class="post-navigation">
            <a href="#" class="prev-post">← Previous Article</a>
            <a href="#" class="next-post">Next Article →</a>
        </div>
    </footer>
</article>
```

## Microdata and Structured Data

### Schema.org Markup
```html
<!-- Article with structured data -->
<article itemscope itemtype="https://schema.org/Article">
    <header>
        <h1 itemprop="headline">How to Build Accessible Web Applications</h1>
        <div class="article-meta">
            <span itemprop="author" itemscope itemtype="https://schema.org/Person">
                <span itemprop="name">Jane Doe</span>
            </span>
            <time itemprop="datePublished" datetime="2024-01-15">January 15, 2024</time>
            <time itemprop="dateModified" datetime="2024-01-16">January 16, 2024</time>
        </div>
    </header>
    
    <div itemprop="articleBody">
        <p>Building accessible web applications is crucial for creating inclusive user experiences...</p>
    </div>
    
    <div itemprop="publisher" itemscope itemtype="https://schema.org/Organization">
        <span itemprop="name">Web Development Blog</span>
        <div itemprop="logo" itemscope itemtype="https://schema.org/ImageObject">
            <img src="logo.jpg" alt="Web Development Blog Logo" itemprop="url">
        </div>
    </div>
</article>

<!-- Product with structured data -->
<div itemscope itemtype="https://schema.org/Product">
    <h1 itemprop="name">Wireless Bluetooth Headphones</h1>
    <img src="headphones.jpg" alt="Wireless Bluetooth Headphones" itemprop="image">
    
    <div itemprop="description">
        High-quality wireless Bluetooth headphones with noise cancellation
    </div>
    
    <div itemprop="offers" itemscope itemtype="https://schema.org/Offer">
        <span itemprop="price" content="99.99">$99.99</span>
        <span itemprop="priceCurrency" content="USD">USD</span>
        <link itemprop="availability" href="https://schema.org/InStock">In Stock
    </div>
    
    <div itemprop="brand" itemscope itemtype="https://schema.org/Brand">
        <span itemprop="name">TechBrand</span>
    </div>
    
    <div itemprop="aggregateRating" itemscope itemtype="https://schema.org/AggregateRating">
        <span itemprop="ratingValue">4.5</span> stars
        <span itemprop="reviewCount">128</span> reviews
    </div>
</div>
```

### JSON-LD Structured Data
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "How to Build Accessible Web Applications",
  "author": {
    "@type": "Person",
    "name": "Jane Doe",
    "url": "https://example.com/author/jane-doe"
  },
  "datePublished": "2024-01-15",
  "dateModified": "2024-01-16",
  "publisher": {
    "@type": "Organization",
    "name": "Web Development Blog",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.jpg"
    }
  },
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://example.com/article/accessible-web-apps"
  },
  "image": {
    "@type": "ImageObject",
    "url": "https://example.com/article-image.jpg",
    "width": 1200,
    "height": 630
  },
  "articleBody": "Building accessible web applications is crucial for creating inclusive user experiences..."
}
</script>
```

## Performance Optimization

### Resource Loading Optimization
```html
<head>
    <!-- Preload critical resources -->
    <link rel="preload" href="critical.css" as="style">
    <link rel="preload" href="hero-image.jpg" as="image">
    <link rel="preload" href="main.js" as="script">
    
    <!-- Prefetch resources for next page -->
    <link rel="prefetch" href="next-page.html">
    <link rel="prefetch" href="next-page.css">
    
    <!-- DNS prefetch for external domains -->
    <link rel="dns-prefetch" href="//fonts.googleapis.com">
    <link rel="dns-prefetch" href="//cdn.example.com">
    
    <!-- Critical CSS inline -->
    <style>
        /* Critical above-the-fold CSS */
        .hero { background: #f0f0f0; }
        .navigation { display: flex; }
    </style>
    
    <!-- Non-critical CSS loaded asynchronously -->
    <link rel="preload" href="non-critical.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="non-critical.css"></noscript>
</head>

<body>
    <!-- Critical content first -->
    <header>
        <nav>
            <!-- Navigation content -->
        </nav>
    </header>
    
    <main>
        <section class="hero">
            <!-- Hero content -->
        </section>
    </main>
    
    <!-- Non-critical content -->
    <aside>
        <!-- Sidebar content -->
    </aside>
    
    <!-- Scripts at the end -->
    <script src="main.js" defer></script>
    <script>
        // Inline critical JavaScript
        document.addEventListener('DOMContentLoaded', function() {
            // Critical functionality
        });
    </script>
</body>
```

## Exercise: Complete Document Structure

Create a complete HTML document with:
- Proper document structure and meta tags
- Semantic HTML organization
- Structured data markup
- Performance optimization
- Accessibility features
- SEO optimization

## Key Takeaways
- Use proper DOCTYPE and document structure
- Include essential meta tags for SEO and social sharing
- Implement semantic HTML for better structure
- Add structured data for rich snippets
- Optimize resource loading for performance
- Follow heading hierarchy for accessibility
- Use microdata or JSON-LD for structured data
- Include security and performance meta tags
- Test with SEO and accessibility tools
- Keep document structure clean and organized
