# Caching Strategies

## Introduction
Caching is a fundamental technique for improving web application performance. This lesson covers browser caching, service workers, CDN optimization, and advanced caching strategies.

## Browser Caching

### HTTP Caching Headers
```javascript
// Server-side caching headers configuration
class CacheHeaders {
    static setCacheHeaders(res, options = {}) {
        const {
            maxAge = 31536000, // 1 year
            staleWhileRevalidate = 86400, // 1 day
            etag = true,
            lastModified = true
        } = options;
        
        // Set Cache-Control header
        res.setHeader('Cache-Control', `public, max-age=${maxAge}, stale-while-revalidate=${staleWhileRevalidate}`);
        
        // Set ETag for validation
        if (etag) {
            const etagValue = this.generateETag(res.body);
            res.setHeader('ETag', etagValue);
        }
        
        // Set Last-Modified header
        if (lastModified) {
            res.setHeader('Last-Modified', new Date().toUTCString());
        }
        
        // Set Vary header for proper caching
        res.setHeader('Vary', 'Accept-Encoding');
    }
    
    static generateETag(body) {
        const crypto = require('crypto');
        return `"${crypto.createHash('md5').update(body).digest('hex')}"`;
    }
    
    static setNoCacheHeaders(res) {
        res.setHeader('Cache-Control', 'no-cache, no-store, must-revalidate');
        res.setHeader('Pragma', 'no-cache');
        res.setHeader('Expires', '0');
    }
    
    static setPrivateCacheHeaders(res, maxAge = 3600) {
        res.setHeader('Cache-Control', `private, max-age=${maxAge}`);
    }
}

// Usage in Express.js
const express = require('express');
const app = express();

// Static assets with long-term caching
app.use('/static', express.static('public', {
    maxAge: '1y',
    etag: true,
    lastModified: true
}));

// API responses with shorter caching
app.get('/api/data', (req, res) => {
    const data = { message: 'Hello World' };
    
    CacheHeaders.setCacheHeaders(res, {
        maxAge: 3600, // 1 hour
        staleWhileRevalidate: 7200 // 2 hours
    });
    
    res.json(data);
});

// Dynamic content with no caching
app.get('/api/user', (req, res) => {
    const user = { id: 1, name: 'John Doe' };
    
    CacheHeaders.setNoCacheHeaders(res);
    res.json(user);
});
```

### Client-Side Caching
```javascript
// Client-side caching utility
class ClientCache {
    constructor(options = {}) {
        this.maxSize = options.maxSize || 100;
        this.maxAge = options.maxAge || 300000; // 5 minutes
        this.cache = new Map();
        this.timers = new Map();
    }
    
    set(key, value, ttl = this.maxAge) {
        // Remove existing timer
        if (this.timers.has(key)) {
            clearTimeout(this.timers.get(key));
        }
        
        // Set timer for expiration
        const timer = setTimeout(() => {
            this.delete(key);
        }, ttl);
        
        this.timers.set(key, timer);
        
        // Add to cache
        this.cache.set(key, {
            value,
            timestamp: Date.now(),
            ttl
        });
        
        // Check cache size
        if (this.cache.size > this.maxSize) {
            const firstKey = this.cache.keys().next().value;
            this.delete(firstKey);
        }
    }
    
    get(key) {
        const item = this.cache.get(key);
        
        if (!item) {
            return null;
        }
        
        // Check if expired
        if (Date.now() - item.timestamp > item.ttl) {
            this.delete(key);
            return null;
        }
        
        return item.value;
    }
    
    delete(key) {
        this.cache.delete(key);
        
        if (this.timers.has(key)) {
            clearTimeout(this.timers.get(key));
            this.timers.delete(key);
        }
    }
    
    clear() {
        this.cache.clear();
        this.timers.forEach(timer => clearTimeout(timer));
        this.timers.clear();
    }
    
    has(key) {
        return this.cache.has(key) && this.get(key) !== null;
    }
    
    size() {
        return this.cache.size;
    }
    
    keys() {
        return Array.from(this.cache.keys());
    }
}

// Usage
const cache = new ClientCache({
    maxSize: 50,
    maxAge: 600000 // 10 minutes
});

// Cache API responses
async function fetchWithCache(url, options = {}) {
    const cacheKey = `${url}-${JSON.stringify(options)}`;
    
    // Check cache first
    const cached = cache.get(cacheKey);
    if (cached) {
        console.log('Cache hit:', url);
        return cached;
    }
    
    // Fetch from network
    console.log('Cache miss:', url);
    const response = await fetch(url, options);
    const data = await response.json();
    
    // Cache the response
    cache.set(cacheKey, data);
    
    return data;
}
```

## Service Worker Caching

### Service Worker Implementation
```javascript
// service-worker.js
const CACHE_NAME = 'app-cache-v1';
const STATIC_CACHE = 'static-cache-v1';
const DYNAMIC_CACHE = 'dynamic-cache-v1';

// Static assets to cache
const STATIC_ASSETS = [
    '/',
    '/index.html',
    '/styles/main.css',
    '/scripts/main.js',
    '/images/logo.png'
];

// Install event
self.addEventListener('install', (event) => {
    console.log('Service Worker installing...');
    
    event.waitUntil(
        caches.open(STATIC_CACHE)
            .then((cache) => {
                console.log('Caching static assets');
                return cache.addAll(STATIC_ASSETS);
            })
            .then(() => {
                console.log('Service Worker installed');
                return self.skipWaiting();
            })
    );
});

// Activate event
self.addEventListener('activate', (event) => {
    console.log('Service Worker activating...');
    
    event.waitUntil(
        caches.keys()
            .then((cacheNames) => {
                return Promise.all(
                    cacheNames.map((cacheName) => {
                        if (cacheName !== STATIC_CACHE && cacheName !== DYNAMIC_CACHE) {
                            console.log('Deleting old cache:', cacheName);
                            return caches.delete(cacheName);
                        }
                    })
                );
            })
            .then(() => {
                console.log('Service Worker activated');
                return self.clients.claim();
            })
    );
});

// Fetch event
self.addEventListener('fetch', (event) => {
    const { request } = event;
    const url = new URL(request.url);
    
    // Handle different types of requests
    if (request.method === 'GET') {
        if (url.origin === location.origin) {
            // Same-origin requests
            event.respondWith(handleSameOriginRequest(request));
        } else {
            // Cross-origin requests
            event.respondWith(handleCrossOriginRequest(request));
        }
    }
});

// Handle same-origin requests
async function handleSameOriginRequest(request) {
    const url = new URL(request.url);
    
    // Static assets - cache first
    if (isStaticAsset(url.pathname)) {
        return cacheFirst(request, STATIC_CACHE);
    }
    
    // API requests - network first
    if (url.pathname.startsWith('/api/')) {
        return networkFirst(request, DYNAMIC_CACHE);
    }
    
    // HTML pages - network first with fallback
    if (request.headers.get('accept').includes('text/html')) {
        return networkFirst(request, DYNAMIC_CACHE);
    }
    
    // Default - cache first
    return cacheFirst(request, DYNAMIC_CACHE);
}

// Handle cross-origin requests
async function handleCrossOriginRequest(request) {
    // Only cache GET requests
    if (request.method !== 'GET') {
        return fetch(request);
    }
    
    // Cache first for cross-origin resources
    return cacheFirst(request, DYNAMIC_CACHE);
}

// Cache first strategy
async function cacheFirst(request, cacheName) {
    try {
        const cachedResponse = await caches.match(request);
        if (cachedResponse) {
            return cachedResponse;
        }
        
        const networkResponse = await fetch(request);
        const cache = await caches.open(cacheName);
        cache.put(request, networkResponse.clone());
        
        return networkResponse;
    } catch (error) {
        console.error('Cache first failed:', error);
        return new Response('Network error', { status: 503 });
    }
}

// Network first strategy
async function networkFirst(request, cacheName) {
    try {
        const networkResponse = await fetch(request);
        const cache = await caches.open(cacheName);
        cache.put(request, networkResponse.clone());
        
        return networkResponse;
    } catch (error) {
        console.error('Network first failed:', error);
        
        const cachedResponse = await caches.match(request);
        if (cachedResponse) {
            return cachedResponse;
        }
        
        return new Response('Network error', { status: 503 });
    }
}

// Check if asset is static
function isStaticAsset(pathname) {
    const staticExtensions = ['.css', '.js', '.png', '.jpg', '.jpeg', '.gif', '.svg', '.woff', '.woff2'];
    return staticExtensions.some(ext => pathname.endsWith(ext));
}

// Register service worker
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('/sw.js')
            .then((registration) => {
                console.log('Service Worker registered:', registration);
            })
            .catch((error) => {
                console.error('Service Worker registration failed:', error);
            });
    });
}
```

### Advanced Service Worker Patterns
```javascript
// Advanced service worker with background sync
class AdvancedServiceWorker {
    constructor() {
        this.setupBackgroundSync();
        this.setupPushNotifications();
        this.setupCacheStrategies();
    }
    
    setupBackgroundSync() {
        self.addEventListener('sync', (event) => {
            if (event.tag === 'background-sync') {
                event.waitUntil(this.doBackgroundSync());
            }
        });
    }
    
    setupPushNotifications() {
        self.addEventListener('push', (event) => {
            if (event.data) {
                const data = event.data.json();
                const options = {
                    body: data.body,
                    icon: data.icon,
                    badge: data.badge,
                    tag: data.tag,
                    actions: data.actions
                };
                
                event.waitUntil(
                    self.registration.showNotification(data.title, options)
                );
            }
        });
    }
    
    setupCacheStrategies() {
        self.addEventListener('fetch', (event) => {
            const { request } = event;
            const url = new URL(request.url);
            
            // Stale while revalidate for API calls
            if (url.pathname.startsWith('/api/')) {
                event.respondWith(this.staleWhileRevalidate(request));
            }
            
            // Network only for critical requests
            if (url.pathname.startsWith('/api/critical/')) {
                event.respondWith(this.networkOnly(request));
            }
        });
    }
    
    async staleWhileRevalidate(request) {
        const cache = await caches.open('api-cache');
        const cachedResponse = await cache.match(request);
        
        const fetchPromise = fetch(request).then((networkResponse) => {
            cache.put(request, networkResponse.clone());
            return networkResponse;
        });
        
        return cachedResponse || fetchPromise;
    }
    
    async networkOnly(request) {
        try {
            return await fetch(request);
        } catch (error) {
            return new Response('Network error', { status: 503 });
        }
    }
    
    async doBackgroundSync() {
        // Perform background sync operations
        console.log('Performing background sync...');
        
        // Sync offline data
        await this.syncOfflineData();
        
        // Update cache
        await this.updateCache();
    }
    
    async syncOfflineData() {
        // Sync offline data with server
        const offlineData = await this.getOfflineData();
        
        for (const data of offlineData) {
            try {
                await fetch('/api/sync', {
                    method: 'POST',
                    body: JSON.stringify(data)
                });
                
                // Remove from offline storage
                await this.removeOfflineData(data.id);
            } catch (error) {
                console.error('Sync failed:', error);
            }
        }
    }
    
    async updateCache() {
        // Update cache with fresh data
        const cache = await caches.open('api-cache');
        const requests = await cache.keys();
        
        for (const request of requests) {
            try {
                const response = await fetch(request);
                cache.put(request, response);
            } catch (error) {
                console.error('Cache update failed:', error);
            }
        }
    }
    
    async getOfflineData() {
        // Get offline data from IndexedDB
        return [];
    }
    
    async removeOfflineData(id) {
        // Remove offline data from IndexedDB
    }
}

// Initialize advanced service worker
new AdvancedServiceWorker();
```

## CDN and Edge Caching

### CDN Configuration
```javascript
// CDN configuration and optimization
class CDNOptimizer {
    constructor() {
        this.cdnUrl = 'https://cdn.example.com';
        this.optimizations = {
            imageOptimization: true,
            compression: true,
            minification: true,
            versioning: true
        };
    }
    
    // Optimize image URLs
    optimizeImageUrl(url, options = {}) {
        const {
            width,
            height,
            quality = 80,
            format = 'auto',
            fit = 'cover'
        } = options;
        
        const params = new URLSearchParams();
        
        if (width) params.set('w', width);
        if (height) params.set('h', height);
        params.set('q', quality);
        params.set('f', format);
        params.set('fit', fit);
        
        return `${this.cdnUrl}/images/${url}?${params.toString()}`;
    }
    
    // Optimize CSS URLs
    optimizeCSSUrl(url, options = {}) {
        const {
            minify = true,
            version = '1.0.0'
        } = options;
        
        const params = new URLSearchParams();
        
        if (minify) params.set('minify', 'true');
        if (version) params.set('v', version);
        
        return `${this.cdnUrl}/css/${url}?${params.toString()}`;
    }
    
    // Optimize JavaScript URLs
    optimizeJSUrl(url, options = {}) {
        const {
            minify = true,
            version = '1.0.0'
        } = options;
        
        const params = new URLSearchParams();
        
        if (minify) params.set('minify', 'true');
        if (version) params.set('v', version);
        
        return `${this.cdnUrl}/js/${url}?${params.toString()}`;
    }
    
    // Preload critical resources
    preloadCriticalResources(resources) {
        resources.forEach(resource => {
            const link = document.createElement('link');
            link.rel = 'preload';
            link.href = resource.url;
            link.as = resource.type;
            
            if (resource.crossorigin) {
                link.crossOrigin = resource.crossorigin;
            }
            
            document.head.appendChild(link);
        });
    }
    
    // Prefetch non-critical resources
    prefetchResources(resources) {
        resources.forEach(resource => {
            const link = document.createElement('link');
            link.rel = 'prefetch';
            link.href = resource.url;
            
            document.head.appendChild(link);
        });
    }
}

// Usage
const cdnOptimizer = new CDNOptimizer();

// Optimize images
const optimizedImageUrl = cdnOptimizer.optimizeImageUrl('hero.jpg', {
    width: 1200,
    height: 600,
    quality: 90
});

// Preload critical resources
cdnOptimizer.preloadCriticalResources([
    { url: '/css/critical.css', type: 'style' },
    { url: '/js/critical.js', type: 'script' },
    { url: '/fonts/main.woff2', type: 'font', crossorigin: 'anonymous' }
]);

// Prefetch non-critical resources
cdnOptimizer.prefetchResources([
    { url: '/css/non-critical.css' },
    { url: '/js/non-critical.js' }
]);
```

## Exercise: Caching System

Create a comprehensive caching system with:
- HTTP caching headers configuration
- Client-side caching utilities
- Service worker implementation
- Advanced service worker patterns
- CDN optimization
- Cache invalidation strategies

## Key Takeaways
- Use appropriate HTTP caching headers for different content types
- Implement client-side caching for frequently accessed data
- Use service workers for offline functionality and caching
- Implement different caching strategies based on content type
- Optimize CDN usage for better performance
- Monitor cache hit rates and performance
- Implement cache invalidation strategies
- Test caching behavior across different browsers
- Use background sync for offline data synchronization
- Monitor and optimize cache performance
