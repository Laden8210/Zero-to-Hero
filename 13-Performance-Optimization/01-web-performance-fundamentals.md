# Web Performance Fundamentals

## Introduction
Web performance is crucial for user experience and business success. This lesson covers Core Web Vitals, performance metrics, optimization techniques, and monitoring strategies.

## Core Web Vitals

### Understanding Core Web Vitals
```javascript
// Core Web Vitals measurement
class WebVitalsMonitor {
    constructor() {
        this.metrics = {};
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        // Largest Contentful Paint (LCP)
        this.measureLCP();
        
        // First Input Delay (FID)
        this.measureFID();
        
        // Cumulative Layout Shift (CLS)
        this.measureCLS();
        
        // First Contentful Paint (FCP)
        this.measureFCP();
        
        // Time to Interactive (TTI)
        this.measureTTI();
    }
    
    measureLCP() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            const lastEntry = entries[entries.length - 1];
            
            this.metrics.lcp = {
                value: lastEntry.startTime,
                element: lastEntry.element,
                url: lastEntry.url
            };
            
            console.log('LCP:', this.metrics.lcp);
        });
        
        observer.observe({ entryTypes: ['largest-contentful-paint'] });
    }
    
    measureFID() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                this.metrics.fid = {
                    value: entry.processingStart - entry.startTime,
                    target: entry.target
                };
                
                console.log('FID:', this.metrics.fid);
            });
        });
        
        observer.observe({ entryTypes: ['first-input'] });
    }
    
    measureCLS() {
        let clsValue = 0;
        let clsEntries = [];
        
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (!entry.hadRecentInput) {
                    clsValue += entry.value;
                    clsEntries.push(entry);
                }
            });
            
            this.metrics.cls = {
                value: clsValue,
                entries: clsEntries
            };
            
            console.log('CLS:', this.metrics.cls);
        });
        
        observer.observe({ entryTypes: ['layout-shift'] });
    }
    
    measureFCP() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                this.metrics.fcp = {
                    value: entry.startTime
                };
                
                console.log('FCP:', this.metrics.fcp);
            });
        });
        
        observer.observe({ entryTypes: ['paint'] });
    }
    
    measureTTI() {
        // TTI is more complex and requires additional logic
        // This is a simplified version
        const tti = performance.timing.domContentLoadedEventEnd - performance.timing.navigationStart;
        
        this.metrics.tti = {
            value: tti
        };
        
        console.log('TTI:', this.metrics.tti);
    }
    
    getMetrics() {
        return this.metrics;
    }
    
    // Send metrics to analytics
    sendToAnalytics() {
        if (window.gtag) {
            Object.entries(this.metrics).forEach(([key, metric]) => {
                window.gtag('event', key, {
                    value: Math.round(metric.value),
                    custom_map: { metric_name: key }
                });
            });
        }
    }
}

// Usage
const webVitals = new WebVitalsMonitor();

// Send metrics after page load
window.addEventListener('load', () => {
    setTimeout(() => {
        webVitals.sendToAnalytics();
    }, 1000);
});
```

**Code Explanation:**
- `class WebVitalsMonitor`: Creates a class to monitor Core Web Vitals metrics
- `constructor()`: Initializes metrics object and sets up monitoring
- `new PerformanceObserver((list) => {...})`: Creates observer for performance entries
- `observer.observe({ entryTypes: ['largest-contentful-paint'] })`: Observes LCP events
- `entry.startTime`: Time when the largest contentful paint occurred
- `entry.element`: The element that triggered the LCP
- `entry.processingStart - entry.startTime`: Calculates FID delay
- `entry.hadRecentInput`: Checks if layout shift was caused by user input
- `clsValue += entry.value`: Accumulates layout shift scores
- `performance.timing.domContentLoadedEventEnd`: Gets DOM content loaded time
- `performance.timing.navigationStart`: Gets navigation start time
- `window.gtag('event', key, {...})`: Sends metrics to Google Analytics
- `window.addEventListener('load', () => {...})`: Waits for page load completion

**Benefits:**
- Provides real-time performance monitoring
- Helps identify performance bottlenecks
- Enables data-driven optimization decisions
- Tracks Core Web Vitals for SEO and user experience
- Supports performance regression detection

### Performance Budget Monitoring
```javascript
// Performance budget monitoring
class PerformanceBudget {
    constructor(budget) {
        this.budget = budget;
        this.violations = [];
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        // Monitor resource sizes
        this.monitorResourceSizes();
        
        // Monitor network requests
        this.monitorNetworkRequests();
        
        // Monitor JavaScript execution time
        this.monitorJavaScriptExecution();
        
        // Monitor CSS performance
        this.monitorCSSPerformance();
    }
    
    monitorResourceSizes() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                const size = entry.transferSize || entry.decodedBodySize;
                const type = entry.initiatorType;
                
                if (this.budget[type] && size > this.budget[type]) {
                    this.violations.push({
                        type: 'resource_size',
                        resource: entry.name,
                        size: size,
                        budget: this.budget[type],
                        severity: 'warning'
                    });
                }
            });
        });
        
        observer.observe({ entryTypes: ['resource'] });
    }
    
    monitorNetworkRequests() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                const duration = entry.responseEnd - entry.requestStart;
                
                if (duration > this.budget.network_timeout) {
                    this.violations.push({
                        type: 'network_timeout',
                        resource: entry.name,
                        duration: duration,
                        budget: this.budget.network_timeout,
                        severity: 'error'
                    });
                }
            });
        });
        
        observer.observe({ entryTypes: ['navigation', 'resource'] });
    }
    
    monitorJavaScriptExecution() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'measure') {
                    const duration = entry.duration;
                    
                    if (duration > this.budget.js_execution_time) {
                        this.violations.push({
                            type: 'js_execution_time',
                            measure: entry.name,
                            duration: duration,
                            budget: this.budget.js_execution_time,
                            severity: 'warning'
                        });
                    }
                }
            });
        });
        
        observer.observe({ entryTypes: ['measure'] });
    }
    
    monitorCSSPerformance() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'paint') {
                    const duration = entry.startTime;
                    
                    if (entry.name === 'first-contentful-paint' && duration > this.budget.fcp) {
                        this.violations.push({
                            type: 'fcp_violation',
                            duration: duration,
                            budget: this.budget.fcp,
                            severity: 'error'
                        });
                    }
                }
            });
        });
        
        observer.observe({ entryTypes: ['paint'] });
    }
    
    getViolations() {
        return this.violations;
    }
    
    reportViolations() {
        if (this.violations.length > 0) {
            console.warn('Performance budget violations:', this.violations);
            
            // Send to monitoring service
            if (window.Sentry) {
                window.Sentry.captureMessage('Performance budget violations', {
                    level: 'warning',
                    extra: this.violations
                });
            }
        }
    }
}

// Usage
const budget = {
    script: 100000, // 100KB
    stylesheet: 50000, // 50KB
    image: 200000, // 200KB
    network_timeout: 3000, // 3 seconds
    js_execution_time: 100, // 100ms
    fcp: 2000 // 2 seconds
};

const performanceBudget = new PerformanceBudget(budget);

// Report violations after page load
window.addEventListener('load', () => {
    setTimeout(() => {
        performanceBudget.reportViolations();
    }, 2000);
});
```

## Performance Optimization Techniques

### Resource Optimization
```javascript
// Resource optimization utilities
class ResourceOptimizer {
    constructor() {
        this.setupOptimizations();
    }
    
    setupOptimizations() {
        // Lazy load images
        this.lazyLoadImages();
        
        // Preload critical resources
        this.preloadCriticalResources();
        
        // Optimize font loading
        this.optimizeFontLoading();
        
        // Compress images
        this.compressImages();
    }
    
    lazyLoadImages() {
        const images = document.querySelectorAll('img[data-src]');
        
        const imageObserver = new IntersectionObserver((entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src;
                    img.classList.remove('lazy');
                    imageObserver.unobserve(img);
                }
            });
        });
        
        images.forEach((img) => imageObserver.observe(img));
    }
    
    preloadCriticalResources() {
        const criticalResources = [
            { href: '/css/critical.css', as: 'style' },
            { href: '/js/critical.js', as: 'script' },
            { href: '/fonts/main.woff2', as: 'font', crossorigin: 'anonymous' }
        ];
        
        criticalResources.forEach((resource) => {
            const link = document.createElement('link');
            link.rel = 'preload';
            link.href = resource.href;
            link.as = resource.as;
            
            if (resource.crossorigin) {
                link.crossOrigin = resource.crossorigin;
            }
            
            document.head.appendChild(link);
        });
    }
    
    optimizeFontLoading() {
        // Use font-display: swap
        const style = document.createElement('style');
        style.textContent = `
            @font-face {
                font-family: 'MainFont';
                src: url('/fonts/main.woff2') format('woff2');
                font-display: swap;
            }
        `;
        document.head.appendChild(style);
        
        // Preload fonts
        const fontLink = document.createElement('link');
        fontLink.rel = 'preload';
        fontLink.href = '/fonts/main.woff2';
        fontLink.as = 'font';
        fontLink.crossOrigin = 'anonymous';
        document.head.appendChild(fontLink);
    }
    
    compressImages() {
        const images = document.querySelectorAll('img');
        
        images.forEach((img) => {
            // Add loading="lazy" for non-critical images
            if (!img.hasAttribute('loading')) {
                img.loading = 'lazy';
            }
            
            // Add decoding="async" for better performance
            if (!img.hasAttribute('decoding')) {
                img.decoding = 'async';
            }
        });
    }
    
    // Service Worker for caching
    async setupServiceWorker() {
        if ('serviceWorker' in navigator) {
            try {
                const registration = await navigator.serviceWorker.register('/sw.js');
                console.log('Service Worker registered:', registration);
            } catch (error) {
                console.error('Service Worker registration failed:', error);
            }
        }
    }
}

// Usage
const resourceOptimizer = new ResourceOptimizer();
resourceOptimizer.setupServiceWorker();
```

### JavaScript Performance Optimization
```javascript
// JavaScript performance optimization
class JavaScriptOptimizer {
    constructor() {
        this.setupOptimizations();
    }
    
    setupOptimizations() {
        // Debounce scroll events
        this.debounceScrollEvents();
        
        // Throttle resize events
        this.throttleResizeEvents();
        
        // Optimize animations
        this.optimizeAnimations();
        
        // Use requestIdleCallback
        this.useIdleCallback();
    }
    
    debounceScrollEvents() {
        let scrollTimeout;
        
        const debouncedScroll = () => {
            clearTimeout(scrollTimeout);
            scrollTimeout = setTimeout(() => {
                // Handle scroll event
                this.handleScroll();
            }, 16); // ~60fps
        };
        
        window.addEventListener('scroll', debouncedScroll, { passive: true });
    }
    
    throttleResizeEvents() {
        let resizeTimeout;
        
        const throttledResize = () => {
            if (!resizeTimeout) {
                resizeTimeout = setTimeout(() => {
                    this.handleResize();
                    resizeTimeout = null;
                }, 100);
            }
        };
        
        window.addEventListener('resize', throttledResize);
    }
    
    optimizeAnimations() {
        // Use transform and opacity for animations
        const animatedElements = document.querySelectorAll('.animate');
        
        animatedElements.forEach((element) => {
            element.style.willChange = 'transform, opacity';
            
            // Remove will-change after animation
            element.addEventListener('animationend', () => {
                element.style.willChange = 'auto';
            });
        });
    }
    
    useIdleCallback() {
        if ('requestIdleCallback' in window) {
            requestIdleCallback(() => {
                // Perform non-critical tasks during idle time
                this.performIdleTasks();
            });
        } else {
            // Fallback for browsers without requestIdleCallback
            setTimeout(() => {
                this.performIdleTasks();
            }, 0);
        }
    }
    
    handleScroll() {
        // Optimized scroll handling
        const scrollTop = window.pageYOffset;
        
        // Use transform instead of changing position
        const header = document.querySelector('.header');
        if (header) {
            header.style.transform = `translateY(${scrollTop > 100 ? -100 : 0}px)`;
        }
    }
    
    handleResize() {
        // Optimized resize handling
        const width = window.innerWidth;
        const height = window.innerHeight;
        
        // Update layout based on new dimensions
        this.updateLayout(width, height);
    }
    
    performIdleTasks() {
        // Perform non-critical tasks during idle time
        console.log('Performing idle tasks...');
        
        // Prefetch next page resources
        this.prefetchNextPage();
        
        // Clean up unused resources
        this.cleanupResources();
    }
    
    prefetchNextPage() {
        const nextPageLink = document.querySelector('a[data-prefetch]');
        if (nextPageLink) {
            const link = document.createElement('link');
            link.rel = 'prefetch';
            link.href = nextPageLink.href;
            document.head.appendChild(link);
        }
    }
    
    cleanupResources() {
        // Remove unused event listeners
        // Clean up DOM references
        // Free up memory
    }
    
    updateLayout(width, height) {
        // Update layout based on new dimensions
        console.log(`Layout updated: ${width}x${height}`);
    }
}

// Usage
const jsOptimizer = new JavaScriptOptimizer();
```

## Performance Monitoring

### Real User Monitoring (RUM)
```javascript
// Real User Monitoring
class RUMMonitor {
    constructor() {
        this.metrics = {};
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        // Monitor page load performance
        this.monitorPageLoad();
        
        // Monitor user interactions
        this.monitorUserInteractions();
        
        // Monitor errors
        this.monitorErrors();
        
        // Monitor resource performance
        this.monitorResourcePerformance();
    }
    
    monitorPageLoad() {
        window.addEventListener('load', () => {
            setTimeout(() => {
                const navigation = performance.getEntriesByType('navigation')[0];
                
                this.metrics.pageLoad = {
                    dns: navigation.domainLookupEnd - navigation.domainLookupStart,
                    tcp: navigation.connectEnd - navigation.connectStart,
                    request: navigation.responseEnd - navigation.requestStart,
                    response: navigation.responseEnd - navigation.responseStart,
                    dom: navigation.domContentLoadedEventEnd - navigation.navigationStart,
                    load: navigation.loadEventEnd - navigation.navigationStart
                };
                
                this.sendMetrics();
            }, 0);
        });
    }
    
    monitorUserInteractions() {
        let interactionCount = 0;
        let totalInteractionTime = 0;
        
        const trackInteraction = (event) => {
            interactionCount++;
            const startTime = performance.now();
            
            requestAnimationFrame(() => {
                const endTime = performance.now();
                totalInteractionTime += endTime - startTime;
            });
        };
        
        ['click', 'keydown', 'touchstart'].forEach(eventType => {
            document.addEventListener(eventType, trackInteraction, { passive: true });
        });
        
        // Send interaction metrics periodically
        setInterval(() => {
            if (interactionCount > 0) {
                this.metrics.userInteractions = {
                    count: interactionCount,
                    averageTime: totalInteractionTime / interactionCount
                };
                
                this.sendMetrics();
            }
        }, 30000); // Every 30 seconds
    }
    
    monitorErrors() {
        window.addEventListener('error', (event) => {
            this.metrics.errors = this.metrics.errors || [];
            this.metrics.errors.push({
                message: event.message,
                filename: event.filename,
                lineno: event.lineno,
                colno: event.colno,
                timestamp: Date.now()
            });
            
            this.sendMetrics();
        });
        
        window.addEventListener('unhandledrejection', (event) => {
            this.metrics.errors = this.metrics.errors || [];
            this.metrics.errors.push({
                type: 'unhandledrejection',
                reason: event.reason,
                timestamp: Date.now()
            });
            
            this.sendMetrics();
        });
    }
    
    monitorResourcePerformance() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'resource') {
                    this.metrics.resources = this.metrics.resources || [];
                    this.metrics.resources.push({
                        name: entry.name,
                        duration: entry.duration,
                        size: entry.transferSize,
                        type: entry.initiatorType
                    });
                }
            });
        });
        
        observer.observe({ entryTypes: ['resource'] });
    }
    
    sendMetrics() {
        // Send metrics to analytics service
        if (window.gtag) {
            window.gtag('event', 'performance_metrics', {
                custom_map: {
                    page_load: this.metrics.pageLoad,
                    user_interactions: this.metrics.userInteractions,
                    errors: this.metrics.errors,
                    resources: this.metrics.resources
                }
            });
        }
        
        // Send to custom analytics endpoint
        fetch('/api/analytics/performance', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(this.metrics)
        }).catch(error => {
            console.error('Failed to send performance metrics:', error);
        });
    }
}

// Usage
const rumMonitor = new RUMMonitor();
```

## Exercise: Performance Optimization System

Create a comprehensive performance optimization system with:
- Core Web Vitals monitoring
- Performance budget enforcement
- Resource optimization techniques
- JavaScript performance optimization
- Real User Monitoring (RUM)
- Performance reporting and analytics

## Key Takeaways
- Monitor Core Web Vitals for user experience metrics
- Set up performance budgets to prevent regressions
- Optimize resources with lazy loading and preloading
- Use performance optimization techniques for JavaScript
- Implement Real User Monitoring for production insights
- Use browser APIs like PerformanceObserver for monitoring
- Optimize animations with transform and opacity
- Use requestIdleCallback for non-critical tasks
- Monitor and report performance metrics
- Test performance optimizations across different devices
