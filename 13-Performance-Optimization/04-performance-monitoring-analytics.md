# Performance Monitoring and Analytics

## Introduction
Performance monitoring and analytics are essential for maintaining optimal web application performance. This lesson covers performance monitoring tools, analytics implementation, and optimization strategies.

## Performance Monitoring Tools

### Web Vitals Monitoring
```javascript
// Comprehensive Web Vitals monitoring
class WebVitalsMonitor {
    constructor() {
        this.metrics = {};
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        // Core Web Vitals
        this.measureLCP();
        this.measureFID();
        this.measureCLS();
        
        // Additional metrics
        this.measureFCP();
        this.measureTTI();
        this.measureTBT();
        
        // Custom metrics
        this.measureCustomMetrics();
    }
    
    measureLCP() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            const lastEntry = entries[entries.length - 1];
            
            this.metrics.lcp = {
                value: lastEntry.startTime,
                element: lastEntry.element,
                url: lastEntry.url,
                timestamp: Date.now()
            };
            
            this.reportMetric('lcp', this.metrics.lcp);
        });
        
        observer.observe({ entryTypes: ['largest-contentful-paint'] });
    }
    
    measureFID() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                this.metrics.fid = {
                    value: entry.processingStart - entry.startTime,
                    target: entry.target,
                    timestamp: Date.now()
                };
                
                this.reportMetric('fid', this.metrics.fid);
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
                entries: clsEntries,
                timestamp: Date.now()
            };
            
            this.reportMetric('cls', this.metrics.cls);
        });
        
        observer.observe({ entryTypes: ['layout-shift'] });
    }
    
    measureFCP() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.name === 'first-contentful-paint') {
                    this.metrics.fcp = {
                        value: entry.startTime,
                        timestamp: Date.now()
                    };
                    
                    this.reportMetric('fcp', this.metrics.fcp);
                }
            });
        });
        
        observer.observe({ entryTypes: ['paint'] });
    }
    
    measureTTI() {
        // TTI measurement requires more complex logic
        const tti = performance.timing.domContentLoadedEventEnd - performance.timing.navigationStart;
        
        this.metrics.tti = {
            value: tti,
            timestamp: Date.now()
        };
        
        this.reportMetric('tti', this.metrics.tti);
    }
    
    measureTBT() {
        // Total Blocking Time measurement
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            let tbt = 0;
            
            entries.forEach((entry) => {
                if (entry.duration > 50) {
                    tbt += entry.duration - 50;
                }
            });
            
            this.metrics.tbt = {
                value: tbt,
                timestamp: Date.now()
            };
            
            this.reportMetric('tbt', this.metrics.tbt);
        });
        
        observer.observe({ entryTypes: ['longtask'] });
    }
    
    measureCustomMetrics() {
        // Custom metrics for specific use cases
        this.measurePageLoadTime();
        this.measureResourceLoadTimes();
        this.measureUserInteractions();
    }
    
    measurePageLoadTime() {
        window.addEventListener('load', () => {
            const navigation = performance.getEntriesByType('navigation')[0];
            
            this.metrics.pageLoad = {
                dns: navigation.domainLookupEnd - navigation.domainLookupStart,
                tcp: navigation.connectEnd - navigation.connectStart,
                request: navigation.responseEnd - navigation.requestStart,
                response: navigation.responseEnd - navigation.responseStart,
                dom: navigation.domContentLoadedEventEnd - navigation.navigationStart,
                load: navigation.loadEventEnd - navigation.navigationStart,
                timestamp: Date.now()
            };
            
            this.reportMetric('pageLoad', this.metrics.pageLoad);
        });
    }
    
    measureResourceLoadTimes() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'resource') {
                    const resourceMetric = {
                        name: entry.name,
                        duration: entry.duration,
                        size: entry.transferSize || entry.decodedBodySize,
                        type: entry.initiatorType,
                        timestamp: Date.now()
                    };
                    
                    this.reportMetric('resource', resourceMetric);
                }
            });
        });
        
        observer.observe({ entryTypes: ['resource'] });
    }
    
    measureUserInteractions() {
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
        
        // Report interaction metrics periodically
        setInterval(() => {
            if (interactionCount > 0) {
                const interactionMetric = {
                    count: interactionCount,
                    averageTime: totalInteractionTime / interactionCount,
                    timestamp: Date.now()
                };
                
                this.reportMetric('userInteractions', interactionMetric);
            }
        }, 30000);
    }
    
    reportMetric(name, metric) {
        // Send to analytics service
        if (window.gtag) {
            window.gtag('event', name, {
                value: Math.round(metric.value),
                custom_map: { metric_name: name }
            });
        }
        
        // Send to custom analytics endpoint
        this.sendToAnalytics(name, metric);
    }
    
    sendToAnalytics(name, metric) {
        fetch('/api/analytics/performance', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                name,
                metric,
                url: window.location.href,
                userAgent: navigator.userAgent,
                timestamp: Date.now()
            })
        }).catch(error => {
            console.error('Failed to send performance metrics:', error);
        });
    }
    
    getMetrics() {
        return this.metrics;
    }
}

// Usage
const webVitalsMonitor = new WebVitalsMonitor();
```

### Performance Budget Monitoring
```javascript
// Performance budget monitoring and alerting
class PerformanceBudget {
    constructor(budget) {
        this.budget = budget;
        this.violations = [];
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        this.monitorResourceSizes();
        this.monitorNetworkRequests();
        this.monitorJavaScriptExecution();
        this.monitorCSSPerformance();
        this.monitorWebVitals();
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
                        severity: 'warning',
                        timestamp: Date.now()
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
                        severity: 'error',
                        timestamp: Date.now()
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
                            severity: 'warning',
                            timestamp: Date.now()
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
                            severity: 'error',
                            timestamp: Date.now()
                        });
                    }
                }
            });
        });
        
        observer.observe({ entryTypes: ['paint'] });
    }
    
    monitorWebVitals() {
        // Monitor Core Web Vitals
        const webVitals = ['lcp', 'fid', 'cls'];
        
        webVitals.forEach(vital => {
            if (this.budget[vital]) {
                this.monitorWebVital(vital);
            }
        });
    }
    
    monitorWebVital(vital) {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                const value = entry.startTime || entry.value;
                
                if (value > this.budget[vital]) {
                    this.violations.push({
                        type: `${vital}_violation`,
                        value: value,
                        budget: this.budget[vital],
                        severity: 'error',
                        timestamp: Date.now()
                    });
                }
            });
        });
        
        observer.observe({ entryTypes: [vital] });
    }
    
    getViolations() {
        return this.violations;
    }
    
    reportViolations() {
        if (this.violations.length > 0) {
            console.warn('Performance budget violations:', this.violations);
            
            // Send to monitoring service
            this.sendViolationsToMonitoring();
        }
    }
    
    sendViolationsToMonitoring() {
        fetch('/api/analytics/violations', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                violations: this.violations,
                url: window.location.href,
                timestamp: Date.now()
            })
        }).catch(error => {
            console.error('Failed to send violations:', error);
        });
    }
}

// Usage
const budget = {
    script: 100000, // 100KB
    stylesheet: 50000, // 50KB
    image: 200000, // 200KB
    network_timeout: 3000, // 3 seconds
    js_execution_time: 100, // 100ms
    fcp: 2000, // 2 seconds
    lcp: 2500, // 2.5 seconds
    fid: 100, // 100ms
    cls: 0.1 // 0.1
};

const performanceBudget = new PerformanceBudget(budget);

// Report violations after page load
window.addEventListener('load', () => {
    setTimeout(() => {
        performanceBudget.reportViolations();
    }, 2000);
});
```

## Analytics Implementation

### Performance Analytics Dashboard
```javascript
// Performance analytics dashboard
class PerformanceAnalytics {
    constructor() {
        this.metrics = {};
        this.setupAnalytics();
    }
    
    setupAnalytics() {
        this.setupGoogleAnalytics();
        this.setupCustomAnalytics();
        this.setupRealUserMonitoring();
    }
    
    setupGoogleAnalytics() {
        if (window.gtag) {
            // Track Core Web Vitals
            this.trackWebVitals();
            
            // Track custom events
            this.trackCustomEvents();
        }
    }
    
    setupCustomAnalytics() {
        // Custom analytics implementation
        this.trackPageViews();
        this.trackUserInteractions();
        this.trackPerformanceMetrics();
    }
    
    setupRealUserMonitoring() {
        // RUM implementation
        this.monitorPageLoad();
        this.monitorUserInteractions();
        this.monitorErrors();
    }
    
    trackWebVitals() {
        // Track LCP
        new PerformanceObserver((list) => {
            const entries = list.getEntries();
            const lastEntry = entries[entries.length - 1];
            
            window.gtag('event', 'lcp', {
                value: Math.round(lastEntry.startTime),
                custom_map: { metric_name: 'lcp' }
            });
        }).observe({ entryTypes: ['largest-contentful-paint'] });
        
        // Track FID
        new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                window.gtag('event', 'fid', {
                    value: Math.round(entry.processingStart - entry.startTime),
                    custom_map: { metric_name: 'fid' }
                });
            });
        }).observe({ entryTypes: ['first-input'] });
        
        // Track CLS
        let clsValue = 0;
        new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (!entry.hadRecentInput) {
                    clsValue += entry.value;
                }
            });
            
            window.gtag('event', 'cls', {
                value: Math.round(clsValue * 1000),
                custom_map: { metric_name: 'cls' }
            });
        }).observe({ entryTypes: ['layout-shift'] });
    }
    
    trackCustomEvents() {
        // Track custom performance events
        this.trackResourceLoadTimes();
        this.trackUserInteractions();
        this.trackPageLoadTimes();
    }
    
    trackResourceLoadTimes() {
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'resource') {
                    window.gtag('event', 'resource_load', {
                        resource_name: entry.name,
                        load_time: Math.round(entry.duration),
                        resource_size: entry.transferSize || entry.decodedBodySize,
                        resource_type: entry.initiatorType
                    });
                }
            });
        });
        
        observer.observe({ entryTypes: ['resource'] });
    }
    
    trackUserInteractions() {
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
        
        // Report interaction metrics periodically
        setInterval(() => {
            if (interactionCount > 0) {
                window.gtag('event', 'user_interactions', {
                    interaction_count: interactionCount,
                    average_interaction_time: Math.round(totalInteractionTime / interactionCount)
                });
            }
        }, 30000);
    }
    
    trackPageLoadTimes() {
        window.addEventListener('load', () => {
            const navigation = performance.getEntriesByType('navigation')[0];
            
            window.gtag('event', 'page_load', {
                dns_time: Math.round(navigation.domainLookupEnd - navigation.domainLookupStart),
                tcp_time: Math.round(navigation.connectEnd - navigation.connectStart),
                request_time: Math.round(navigation.responseEnd - navigation.requestStart),
                response_time: Math.round(navigation.responseEnd - navigation.responseStart),
                dom_time: Math.round(navigation.domContentLoadedEventEnd - navigation.navigationStart),
                load_time: Math.round(navigation.loadEventEnd - navigation.navigationStart)
            });
        });
    }
    
    trackPageViews() {
        // Track page views
        window.gtag('config', 'GA_MEASUREMENT_ID', {
            page_title: document.title,
            page_location: window.location.href
        });
    }
    
    trackPerformanceMetrics() {
        // Track custom performance metrics
        this.metrics = {
            pageLoad: this.measurePageLoad(),
            resourceLoad: this.measureResourceLoad(),
            userInteractions: this.measureUserInteractions()
        };
        
        this.sendMetricsToAnalytics();
    }
    
    measurePageLoad() {
        const navigation = performance.getEntriesByType('navigation')[0];
        
        return {
            dns: navigation.domainLookupEnd - navigation.domainLookupStart,
            tcp: navigation.connectEnd - navigation.connectStart,
            request: navigation.responseEnd - navigation.requestStart,
            response: navigation.responseEnd - navigation.responseStart,
            dom: navigation.domContentLoadedEventEnd - navigation.navigationStart,
            load: navigation.loadEventEnd - navigation.navigationStart
        };
    }
    
    measureResourceLoad() {
        const resources = performance.getEntriesByType('resource');
        
        return resources.map(resource => ({
            name: resource.name,
            duration: resource.duration,
            size: resource.transferSize || resource.decodedBodySize,
            type: resource.initiatorType
        }));
    }
    
    measureUserInteractions() {
        // Measure user interactions
        return {
            clicks: 0,
            keydowns: 0,
            touches: 0
        };
    }
    
    sendMetricsToAnalytics() {
        // Send metrics to analytics service
        fetch('/api/analytics/metrics', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                metrics: this.metrics,
                url: window.location.href,
                timestamp: Date.now()
            })
        }).catch(error => {
            console.error('Failed to send metrics:', error);
        });
    }
}

// Usage
const performanceAnalytics = new PerformanceAnalytics();
```

## Exercise: Performance Monitoring System

Create a comprehensive performance monitoring system with:
- Web Vitals monitoring
- Performance budget enforcement
- Analytics implementation
- Real User Monitoring (RUM)
- Performance reporting
- Alerting and notifications

## Key Takeaways
- Monitor Core Web Vitals for user experience metrics
- Implement performance budgets to prevent regressions
- Use analytics to track performance trends
- Monitor real user performance data
- Set up alerting for performance issues
- Use PerformanceObserver API for monitoring
- Track custom metrics for specific use cases
- Implement performance reporting and dashboards
- Monitor performance across different devices and networks
- Use performance data to drive optimization decisions
