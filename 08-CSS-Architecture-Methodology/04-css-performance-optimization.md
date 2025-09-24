# CSS Performance Optimization

## Introduction
CSS performance optimization is crucial for creating fast, responsive web applications. This lesson covers CSS optimization techniques, critical CSS, and performance monitoring strategies.

## CSS Optimization Techniques

### Efficient Selectors
```css
/* Good - Specific and efficient selectors */
.button-primary {
    background: #007bff;
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 8px;
}

.card-title {
    font-size: 1.25rem;
    font-weight: 600;
    margin: 0 0 0.5rem 0;
}

/* Avoid - Inefficient selectors */
/* Bad: Universal selector */
* {
    box-sizing: border-box;
}

/* Bad: Complex descendant selectors */
div.container div.content div.card div.header h2.title {
    color: #333;
}

/* Bad: Attribute selectors without specificity */
[class*="button"] {
    cursor: pointer;
}

/* Better - Optimized selectors */
html {
    box-sizing: border-box;
}

*, *::before, *::after {
    box-sizing: inherit;
}

.card-title {
    color: #333;
}

.button {
    cursor: pointer;
}
```

### CSS Specificity Optimization
```css
/* Low specificity - easy to override */
.button {
    background: #6c757d;
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 8px;
}

/* Medium specificity - component level */
.card .button {
    background: #007bff;
}

/* High specificity - avoid unless necessary */
.card.featured .button.primary {
    background: #28a745;
}

/* Better approach - use CSS custom properties */
.button {
    --button-bg: #6c757d;
    --button-color: white;
    
    background: var(--button-bg);
    color: var(--button-color);
    padding: 0.5rem 1rem;
    border-radius: 8px;
}

.button--primary {
    --button-bg: #007bff;
}

.button--success {
    --button-bg: #28a745;
}
```

### CSS Property Optimization
```css
/* Optimize properties for performance */
.element {
    /* Use transform instead of changing position properties */
    transform: translateX(100px);
    
    /* Use opacity instead of visibility when possible */
    opacity: 0;
    
    /* Use will-change for elements that will animate */
    will-change: transform, opacity;
    
    /* Use contain for layout optimization */
    contain: layout style paint;
    
    /* Use backface-visibility for 3D transforms */
    backface-visibility: hidden;
}

/* Avoid expensive properties */
.expensive {
    /* Avoid: box-shadow with large blur radius */
    box-shadow: 0 0 50px rgba(0, 0, 0, 0.5);
    
    /* Avoid: border-radius with large values */
    border-radius: 50px;
    
    /* Avoid: filter effects */
    filter: blur(10px);
}

/* Optimized alternatives */
.optimized {
    /* Use smaller blur radius */
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    
    /* Use reasonable border-radius */
    border-radius: 8px;
    
    /* Use transform for blur effect */
    transform: scale(0.95);
    opacity: 0.8;
}
```

## Critical CSS Implementation

### Critical CSS Extraction
```css
/* Critical CSS - Above the fold styles */
/* Reset and base styles */
* {
    box-sizing: border-box;
}

html {
    font-size: 16px;
    line-height: 1.5;
}

body {
    font-family: 'Inter', sans-serif;
    color: #333;
    background: #fff;
    margin: 0;
    padding: 0;
}

/* Critical layout */
.header {
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    position: sticky;
    top: 0;
    z-index: 1000;
}

.hero {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    padding: 4rem 0;
    text-align: center;
}

.hero-title {
    font-size: 3rem;
    font-weight: 700;
    margin: 0 0 1rem 0;
}

.hero-subtitle {
    font-size: 1.25rem;
    margin: 0 0 2rem 0;
    opacity: 0.9;
}

.hero-button {
    display: inline-block;
    background: white;
    color: #333;
    padding: 1rem 2rem;
    border-radius: 8px;
    text-decoration: none;
    font-weight: 600;
    transition: transform 0.3s ease;
}

.hero-button:hover {
    transform: translateY(-2px);
}

/* Critical navigation */
.nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 0;
    max-width: 1200px;
    margin: 0 auto;
}

.nav-brand {
    font-size: 1.5rem;
    font-weight: bold;
    color: #333;
}

.nav-menu {
    display: flex;
    gap: 2rem;
    list-style: none;
    margin: 0;
    padding: 0;
}

.nav-link {
    color: #333;
    text-decoration: none;
    font-weight: 500;
    transition: color 0.3s ease;
}

.nav-link:hover {
    color: #007bff;
}
```

### Non-Critical CSS Loading
```css
/* Non-critical CSS - loaded asynchronously */
/* Component styles */
.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    margin-bottom: 2rem;
}

.card-header {
    padding: 1.5rem;
    border-bottom: 1px solid #dee2e6;
    background: #f8f9fa;
}

.card-body {
    padding: 1.5rem;
}

.card-footer {
    padding: 1.5rem;
    border-top: 1px solid #dee2e6;
    background: #f8f9fa;
}

/* Form styles */
.form {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-group {
    margin-bottom: 1.5rem;
}

.form-label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 500;
    color: #333;
}

.form-input {
    width: 100%;
    padding: 0.75rem;
    font-size: 1rem;
    border: 2px solid #dee2e6;
    border-radius: 8px;
    background: white;
    transition: border-color 0.3s ease;
}

.form-input:focus {
    border-color: #007bff;
    outline: none;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.25);
}

/* Button styles */
.button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    font-weight: 500;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.button-primary {
    background: #007bff;
    color: white;
}

.button-primary:hover {
    background: #0056b3;
}

.button-secondary {
    background: #6c757d;
    color: white;
}

.button-secondary:hover {
    background: #545b62;
}
```

### Critical CSS Loading Strategy
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Optimized Page</title>
    
    <!-- Critical CSS inline -->
    <style>
        /* Critical CSS content here */
        * { box-sizing: border-box; }
        body { font-family: 'Inter', sans-serif; margin: 0; }
        .header { background: white; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .hero { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
        /* ... more critical styles ... */
    </style>
    
    <!-- Preload non-critical CSS -->
    <link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="styles.css"></noscript>
    
    <!-- Preload fonts -->
    <link rel="preload" href="fonts/inter.woff2" as="font" type="font/woff2" crossorigin>
</head>
<body>
    <!-- Page content -->
    <header class="header">
        <nav class="nav">
            <div class="nav-brand">Logo</div>
            <ul class="nav-menu">
                <li><a href="#" class="nav-link">Home</a></li>
                <li><a href="#" class="nav-link">About</a></li>
                <li><a href="#" class="nav-link">Contact</a></li>
            </ul>
        </nav>
    </header>
    
    <main>
        <section class="hero">
            <h1 class="hero-title">Welcome to Our Site</h1>
            <p class="hero-subtitle">This is the hero section</p>
            <a href="#" class="hero-button">Get Started</a>
        </section>
        
        <!-- More content -->
    </main>
    
    <script>
        // Load non-critical CSS asynchronously
        function loadCSS(href) {
            const link = document.createElement('link');
            link.rel = 'stylesheet';
            link.href = href;
            document.head.appendChild(link);
        }
        
        // Load CSS after page load
        window.addEventListener('load', function() {
            loadCSS('styles.css');
        });
    </script>
</body>
</html>
```

## CSS Performance Monitoring

### Performance Metrics
```javascript
// CSS performance monitoring
class CSSPerformanceMonitor {
    constructor() {
        this.metrics = {
            cssLoadTime: 0,
            cssSize: 0,
            unusedCSS: 0,
            renderBlockingCSS: 0
        };
        
        this.startMonitoring();
    }
    
    startMonitoring() {
        // Monitor CSS load time
        this.monitorCSSLoadTime();
        
        // Monitor CSS size
        this.monitorCSSSize();
        
        // Monitor unused CSS
        this.monitorUnusedCSS();
        
        // Monitor render-blocking CSS
        this.monitorRenderBlockingCSS();
    }
    
    monitorCSSLoadTime() {
        const startTime = performance.now();
        
        // Monitor when CSS is loaded
        const checkCSSLoaded = () => {
            const stylesheets = document.styleSheets;
            let allLoaded = true;
            
            for (let i = 0; i < stylesheets.length; i++) {
                try {
                    const rules = stylesheets[i].cssRules;
                    if (!rules) {
                        allLoaded = false;
                        break;
                    }
                } catch (e) {
                    allLoaded = false;
                    break;
                }
            }
            
            if (allLoaded) {
                this.metrics.cssLoadTime = performance.now() - startTime;
                console.log(`CSS load time: ${this.metrics.cssLoadTime}ms`);
            } else {
                setTimeout(checkCSSLoaded, 100);
            }
        };
        
        checkCSSLoaded();
    }
    
    monitorCSSSize() {
        let totalSize = 0;
        
        // Calculate CSS file sizes
        const links = document.querySelectorAll('link[rel="stylesheet"]');
        links.forEach(link => {
            fetch(link.href)
                .then(response => response.text())
                .then(css => {
                    totalSize += css.length;
                    this.metrics.cssSize = totalSize;
                    console.log(`Total CSS size: ${totalSize} bytes`);
                });
        });
    }
    
    monitorUnusedCSS() {
        // Simple unused CSS detection
        const usedSelectors = new Set();
        const allSelectors = new Set();
        
        // Get all CSS rules
        const stylesheets = document.styleSheets;
        for (let i = 0; i < stylesheets.length; i++) {
            try {
                const rules = stylesheets[i].cssRules;
                for (let j = 0; j < rules.length; j++) {
                    const rule = rules[j];
                    if (rule.type === CSSRule.STYLE_RULE) {
                        allSelectors.add(rule.selectorText);
                    }
                }
            } catch (e) {
                // Cross-origin stylesheets
            }
        }
        
        // Check which selectors are used
        allSelectors.forEach(selector => {
            try {
                const elements = document.querySelectorAll(selector);
                if (elements.length > 0) {
                    usedSelectors.add(selector);
                }
            } catch (e) {
                // Invalid selector
            }
        });
        
        this.metrics.unusedCSS = allSelectors.size - usedSelectors.size;
        console.log(`Unused CSS selectors: ${this.metrics.unusedCSS}`);
    }
    
    monitorRenderBlockingCSS() {
        // Monitor render-blocking CSS
        const links = document.querySelectorAll('link[rel="stylesheet"]');
        let renderBlockingCount = 0;
        
        links.forEach(link => {
            if (!link.media || link.media === 'all') {
                renderBlockingCount++;
            }
        });
        
        this.metrics.renderBlockingCSS = renderBlockingCount;
        console.log(`Render-blocking CSS files: ${renderBlockingCount}`);
    }
    
    getMetrics() {
        return this.metrics;
    }
}

// Usage
const cssMonitor = new CSSPerformanceMonitor();

// Get metrics after page load
window.addEventListener('load', () => {
    setTimeout(() => {
        console.log('CSS Performance Metrics:', cssMonitor.getMetrics());
    }, 1000);
});
```

### CSS Optimization Tools
```javascript
// CSS optimization utilities
class CSSOptimizer {
    constructor() {
        this.optimizations = {
            removeUnusedCSS: true,
            minifyCSS: true,
            combineCSS: true,
            criticalCSS: true
        };
    }
    
    // Remove unused CSS
    removeUnusedCSS() {
        const usedSelectors = new Set();
        const allSelectors = new Set();
        
        // Get all CSS rules
        const stylesheets = document.styleSheets;
        for (let i = 0; i < stylesheets.length; i++) {
            try {
                const rules = stylesheets[i].cssRules;
                for (let j = 0; j < rules.length; j++) {
                    const rule = rules[j];
                    if (rule.type === CSSRule.STYLE_RULE) {
                        allSelectors.add(rule.selectorText);
                    }
                }
            } catch (e) {
                // Cross-origin stylesheets
            }
        }
        
        // Check which selectors are used
        allSelectors.forEach(selector => {
            try {
                const elements = document.querySelectorAll(selector);
                if (elements.length > 0) {
                    usedSelectors.add(selector);
                }
            } catch (e) {
                // Invalid selector
            }
        });
        
        // Remove unused rules
        const unusedSelectors = Array.from(allSelectors).filter(
            selector => !usedSelectors.has(selector)
        );
        
        console.log(`Found ${unusedSelectors.length} unused CSS selectors`);
        return unusedSelectors;
    }
    
    // Minify CSS
    minifyCSS(css) {
        return css
            .replace(/\/\*[\s\S]*?\*\//g, '') // Remove comments
            .replace(/\s+/g, ' ') // Replace multiple spaces with single space
            .replace(/;\s*}/g, '}') // Remove semicolon before closing brace
            .replace(/\s*{\s*/g, '{') // Remove spaces around opening brace
            .replace(/;\s*/g, ';') // Remove spaces after semicolon
            .replace(/\s*,\s*/g, ',') // Remove spaces around commas
            .trim();
    }
    
    // Combine CSS files
    async combineCSS(urls) {
        const cssContents = await Promise.all(
            urls.map(url => fetch(url).then(response => response.text()))
        );
        
        return cssContents.join('\n');
    }
    
    // Extract critical CSS
    extractCriticalCSS() {
        const criticalSelectors = [
            'html', 'body', 'head', 'title',
            '.header', '.nav', '.hero', '.main',
            '.button', '.form', '.input'
        ];
        
        const criticalCSS = [];
        const stylesheets = document.styleSheets;
        
        for (let i = 0; i < stylesheets.length; i++) {
            try {
                const rules = stylesheets[i].cssRules;
                for (let j = 0; j < rules.length; j++) {
                    const rule = rules[j];
                    if (rule.type === CSSRule.STYLE_RULE) {
                        const selector = rule.selectorText;
                        if (criticalSelectors.some(critical => 
                            selector.includes(critical)
                        )) {
                            criticalCSS.push(rule.cssText);
                        }
                    }
                }
            } catch (e) {
                // Cross-origin stylesheets
            }
        }
        
        return criticalCSS.join('\n');
    }
}

// Usage
const optimizer = new CSSOptimizer();

// Run optimizations
window.addEventListener('load', () => {
    // Remove unused CSS
    const unusedCSS = optimizer.removeUnusedCSS();
    
    // Extract critical CSS
    const criticalCSS = optimizer.extractCriticalCSS();
    console.log('Critical CSS:', criticalCSS);
    
    // Minify CSS
    const minifiedCSS = optimizer.minifyCSS(criticalCSS);
    console.log('Minified CSS:', minifiedCSS);
});
```

## Exercise: CSS Performance Optimization

Create a performance-optimized CSS system with:
- Critical CSS extraction and inline loading
- Non-critical CSS asynchronous loading
- CSS performance monitoring
- Unused CSS detection and removal
- CSS minification and optimization
- Render-blocking CSS elimination

## Key Takeaways
- Use efficient CSS selectors to improve performance
- Implement critical CSS for faster initial page load
- Load non-critical CSS asynchronously
- Monitor CSS performance metrics
- Remove unused CSS to reduce bundle size
- Minify CSS for production builds
- Use CSS custom properties for dynamic theming
- Optimize CSS properties for better rendering performance
- Test CSS performance on real devices
- Use tools to automate CSS optimization
