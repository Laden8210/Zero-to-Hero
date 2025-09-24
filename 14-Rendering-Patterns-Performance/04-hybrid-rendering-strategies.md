# Hybrid Rendering Strategies

## Introduction
Hybrid rendering combines multiple rendering approaches to optimize performance and user experience. This lesson covers hybrid strategies, implementation patterns, and optimization techniques.

## Hybrid Rendering Patterns

### ISR (Incremental Static Regeneration)
```javascript
// ISR implementation with Next.js
import { GetStaticProps, GetStaticPaths } from 'next';
import { useRouter } from 'next/router';

// Page component
function BlogPost({ post, timestamp }) {
    const router = useRouter();
    
    if (router.isFallback) {
        return <div>Loading...</div>;
    }
    
    return (
        <div className="blog-post">
            <h1>{post.title}</h1>
            <div className="meta">
                <span>Published: {post.date}</span>
                <span>Author: {post.author}</span>
                <span>Last updated: {timestamp}</span>
            </div>
            <div className="content">
                <p>{post.content}</p>
            </div>
        </div>
    );
}

// Static generation with ISR
export const getStaticProps = async ({ params }) => {
    const { slug } = params;
    
    try {
        // Fetch post data
        const post = await fetchPost(slug);
        
        if (!post) {
            return {
                notFound: true
            };
        }
        
        return {
            props: {
                post,
                timestamp: new Date().toISOString()
            },
            // Revalidate every 60 seconds
            revalidate: 60
        };
    } catch (error) {
        console.error('Failed to fetch post:', error);
        return {
            notFound: true
        };
    }
};

// Dynamic paths with fallback
export const getStaticPaths = async () => {
    try {
        // Fetch all post slugs
        const posts = await fetchAllPosts();
        const paths = posts.map(post => ({
            params: { slug: post.slug }
        }));
        
        return {
            paths,
            // Enable fallback for new posts
            fallback: true
        };
    } catch (error) {
        console.error('Failed to fetch posts:', error);
        return {
            paths: [],
            fallback: true
        };
    }
};

// API functions
async function fetchPost(slug) {
    const response = await fetch(`${process.env.API_URL}/posts/${slug}`);
    if (!response.ok) {
        return null;
    }
    return response.json();
}

async function fetchAllPosts() {
    const response = await fetch(`${process.env.API_URL}/posts`);
    if (!response.ok) {
        return [];
    }
    return response.json();
}

export default BlogPost;
```

### Edge-Side Rendering (ESR)
```javascript
// Edge-side rendering with Cloudflare Workers
addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request));
});

async function handleRequest(request) {
    const url = new URL(request.url);
    const pathname = url.pathname;
    
    // Check if page should be rendered at edge
    if (shouldRenderAtEdge(pathname)) {
        return await renderAtEdge(request);
    }
    
    // Fallback to origin
    return await fetch(request);
}

async function renderAtEdge(request) {
    const url = new URL(request.url);
    const pathname = url.pathname;
    
    try {
        // Fetch data from origin
        const data = await fetchDataFromOrigin(pathname);
        
        // Render HTML at edge
        const html = await renderHTML(pathname, data);
        
        return new Response(html, {
            headers: {
                'Content-Type': 'text/html',
                'Cache-Control': 'public, max-age=300' // 5 minutes
            }
        });
    } catch (error) {
        console.error('Edge rendering failed:', error);
        
        // Fallback to origin
        return await fetch(request);
    }
}

function shouldRenderAtEdge(pathname) {
    // Define which pages should be rendered at edge
    const edgeRoutes = [
        '/',
        '/about',
        '/contact',
        '/blog'
    ];
    
    return edgeRoutes.includes(pathname) || pathname.startsWith('/blog/');
}

async function fetchDataFromOrigin(pathname) {
    const originUrl = `${ORIGIN_URL}${pathname}`;
    const response = await fetch(originUrl);
    
    if (!response.ok) {
        throw new Error('Failed to fetch data from origin');
    }
    
    return response.json();
}

async function renderHTML(pathname, data) {
    // Simple HTML template rendering
    const template = getTemplate(pathname);
    return template.replace('{{data}}', JSON.stringify(data));
}

function getTemplate(pathname) {
    return `
        <!DOCTYPE html>
        <html>
            <head>
                <title>Edge Rendered Page</title>
                <meta charset="utf-8">
                <meta name="viewport" content="width=device-width, initial-scale=1">
            </head>
            <body>
                <div id="root">
                    <h1>Edge Rendered Content</h1>
                    <div id="data">{{data}}</div>
                </div>
                <script>
                    // Hydrate with edge data
                    window.__EDGE_DATA__ = {{data}};
                </script>
            </body>
        </html>
    `;
}
```

### Progressive Enhancement
```javascript
// Progressive enhancement implementation
class ProgressiveEnhancement {
    constructor() {
        this.setupProgressiveEnhancement();
    }
    
    setupProgressiveEnhancement() {
        // Check if JavaScript is enabled
        if (this.isJavaScriptEnabled()) {
            this.enhanceWithJavaScript();
        } else {
            this.fallbackToStatic();
        }
    }
    
    isJavaScriptEnabled() {
        return typeof window !== 'undefined' && 'querySelector' in document;
    }
    
    enhanceWithJavaScript() {
        // Add JavaScript enhancements
        this.addInteractiveFeatures();
        this.addClientSideRouting();
        this.addRealTimeUpdates();
    }
    
    addInteractiveFeatures() {
        // Add interactive features that require JavaScript
        const buttons = document.querySelectorAll('[data-interactive]');
        
        buttons.forEach(button => {
            button.addEventListener('click', (e) => {
                e.preventDefault();
                this.handleInteractiveAction(button);
            });
        });
    }
    
    addClientSideRouting() {
        // Add client-side routing for better UX
        const links = document.querySelectorAll('a[href^="/"]');
        
        links.forEach(link => {
            link.addEventListener('click', (e) => {
                e.preventDefault();
                this.navigateTo(link.href);
            });
        });
    }
    
    addRealTimeUpdates() {
        // Add real-time updates
        if ('WebSocket' in window) {
            this.setupWebSocket();
        }
    }
    
    handleInteractiveAction(button) {
        const action = button.dataset.action;
        
        switch (action) {
            case 'toggle':
                this.toggleElement(button);
                break;
            case 'modal':
                this.openModal(button);
                break;
            case 'form':
                this.handleFormSubmission(button);
                break;
        }
    }
    
    toggleElement(button) {
        const target = document.querySelector(button.dataset.target);
        if (target) {
            target.classList.toggle('active');
        }
    }
    
    openModal(button) {
        const modalId = button.dataset.modal;
        const modal = document.getElementById(modalId);
        
        if (modal) {
            modal.classList.add('active');
            document.body.classList.add('modal-open');
        }
    }
    
    async handleFormSubmission(button) {
        const form = button.closest('form');
        if (!form) return;
        
        const formData = new FormData(form);
        const data = Object.fromEntries(formData);
        
        try {
            const response = await fetch(form.action, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });
            
            if (response.ok) {
                this.showSuccessMessage('Form submitted successfully!');
            } else {
                this.showErrorMessage('Failed to submit form');
            }
        } catch (error) {
            this.showErrorMessage('Network error');
        }
    }
    
    navigateTo(url) {
        // Client-side navigation
        history.pushState(null, '', url);
        this.loadPage(url);
    }
    
    async loadPage(url) {
        try {
            const response = await fetch(url);
            const html = await response.text();
            
            // Update page content
            const parser = new DOMParser();
            const doc = parser.parseFromString(html, 'text/html');
            const newContent = doc.querySelector('#content');
            const currentContent = document.querySelector('#content');
            
            if (newContent && currentContent) {
                currentContent.innerHTML = newContent.innerHTML;
            }
        } catch (error) {
            console.error('Failed to load page:', error);
        }
    }
    
    setupWebSocket() {
        const ws = new WebSocket('ws://localhost:8080');
        
        ws.onmessage = (event) => {
            const data = JSON.parse(event.data);
            this.handleRealTimeUpdate(data);
        };
    }
    
    handleRealTimeUpdate(data) {
        // Handle real-time updates
        switch (data.type) {
            case 'notification':
                this.showNotification(data.message);
                break;
            case 'data':
                this.updateData(data.payload);
                break;
        }
    }
    
    showNotification(message) {
        const notification = document.createElement('div');
        notification.className = 'notification';
        notification.textContent = message;
        
        document.body.appendChild(notification);
        
        setTimeout(() => {
            notification.remove();
        }, 3000);
    }
    
    updateData(payload) {
        // Update data in real-time
        const dataElement = document.querySelector('#data');
        if (dataElement) {
            dataElement.textContent = JSON.stringify(payload, null, 2);
        }
    }
    
    showSuccessMessage(message) {
        this.showMessage(message, 'success');
    }
    
    showErrorMessage(message) {
        this.showMessage(message, 'error');
    }
    
    showMessage(message, type) {
        const messageElement = document.createElement('div');
        messageElement.className = `message message-${type}`;
        messageElement.textContent = message;
        
        document.body.appendChild(messageElement);
        
        setTimeout(() => {
            messageElement.remove();
        }, 3000);
    }
    
    fallbackToStatic() {
        // Fallback to static functionality
        console.log('Using static fallback');
    }
}

// Initialize progressive enhancement
const progressiveEnhancement = new ProgressiveEnhancement();
```

## Hybrid Rendering Implementation

### Multi-Rendering Strategy
```javascript
// Multi-rendering strategy implementation
class HybridRenderer {
    constructor() {
        this.renderingStrategies = {
            ssr: new SSRRenderer(),
            ssg: new SSGRenderer(),
            csr: new CSRRenderer(),
            isr: new ISRRenderer()
        };
        
        this.routeConfig = this.setupRouteConfig();
    }
    
    setupRouteConfig() {
        return {
            '/': { strategy: 'ssg', revalidate: 3600 },
            '/about': { strategy: 'ssg', revalidate: 86400 },
            '/contact': { strategy: 'ssr' },
            '/blog': { strategy: 'isr', revalidate: 300 },
            '/blog/[slug]': { strategy: 'isr', revalidate: 600 },
            '/dashboard': { strategy: 'csr' },
            '/profile': { strategy: 'csr' }
        };
    }
    
    async render(request) {
        const url = new URL(request.url);
        const pathname = url.pathname;
        
        // Determine rendering strategy
        const strategy = this.determineStrategy(pathname);
        
        // Render using appropriate strategy
        return await this.renderWithStrategy(strategy, request);
    }
    
    determineStrategy(pathname) {
        // Check exact match first
        if (this.routeConfig[pathname]) {
            return this.routeConfig[pathname].strategy;
        }
        
        // Check dynamic routes
        for (const [route, config] of Object.entries(this.routeConfig)) {
            if (this.matchesDynamicRoute(pathname, route)) {
                return config.strategy;
            }
        }
        
        // Default to CSR
        return 'csr';
    }
    
    matchesDynamicRoute(pathname, route) {
        if (!route.includes('[')) return false;
        
        const routePattern = route.replace(/\[.*?\]/g, '[^/]+');
        const regex = new RegExp(`^${routePattern}$`);
        return regex.test(pathname);
    }
    
    async renderWithStrategy(strategy, request) {
        const renderer = this.renderingStrategies[strategy];
        
        if (!renderer) {
            throw new Error(`Unknown rendering strategy: ${strategy}`);
        }
        
        return await renderer.render(request);
    }
}

// SSG Renderer
class SSGRenderer {
    async render(request) {
        const url = new URL(request.url);
        const pathname = url.pathname;
        
        // Check if static file exists
        const staticFile = await this.getStaticFile(pathname);
        
        if (staticFile) {
            return new Response(staticFile, {
                headers: {
                    'Content-Type': 'text/html',
                    'Cache-Control': 'public, max-age=3600'
                }
            });
        }
        
        // Fallback to SSR
        return await this.fallbackToSSR(request);
    }
    
    async getStaticFile(pathname) {
        try {
            const filePath = pathname === '/' ? '/index.html' : `${pathname}/index.html`;
            return await fs.readFile(`dist${filePath}`, 'utf8');
        } catch (error) {
            return null;
        }
    }
    
    async fallbackToSSR(request) {
        const ssrRenderer = new SSRRenderer();
        return await ssrRenderer.render(request);
    }
}

// ISR Renderer
class ISRRenderer {
    constructor() {
        this.cache = new Map();
    }
    
    async render(request) {
        const url = new URL(request.url);
        const pathname = url.pathname;
        
        // Check cache first
        const cached = this.cache.get(pathname);
        
        if (cached && this.isCacheValid(cached)) {
            return new Response(cached.html, {
                headers: {
                    'Content-Type': 'text/html',
                    'Cache-Control': 'public, max-age=300'
                }
            });
        }
        
        // Generate new content
        const html = await this.generateContent(pathname);
        
        // Cache the result
        this.cache.set(pathname, {
            html,
            timestamp: Date.now()
        });
        
        return new Response(html, {
            headers: {
                'Content-Type': 'text/html',
                'Cache-Control': 'public, max-age=300'
            }
        });
    }
    
    isCacheValid(cached) {
        const now = Date.now();
        const age = now - cached.timestamp;
        return age < 300000; // 5 minutes
    }
    
    async generateContent(pathname) {
        // Generate content based on pathname
        const data = await this.fetchData(pathname);
        return this.renderTemplate(pathname, data);
    }
    
    async fetchData(pathname) {
        // Fetch data for the pathname
        const response = await fetch(`${process.env.API_URL}${pathname}`);
        return response.json();
    }
    
    renderTemplate(pathname, data) {
        // Simple template rendering
        return `
            <!DOCTYPE html>
            <html>
                <head>
                    <title>ISR Page</title>
                </head>
                <body>
                    <div id="root">
                        <h1>ISR Content</h1>
                        <div id="data">${JSON.stringify(data)}</div>
                    </div>
                </body>
            </html>
        `;
    }
}

// CSR Renderer
class CSRRenderer {
    async render(request) {
        // Return shell HTML for client-side rendering
        const shell = await this.getShellHTML();
        
        return new Response(shell, {
            headers: {
                'Content-Type': 'text/html',
                'Cache-Control': 'public, max-age=0'
            }
        });
    }
    
    async getShellHTML() {
        return `
            <!DOCTYPE html>
            <html>
                <head>
                    <title>CSR App</title>
                </head>
                <body>
                    <div id="root">
                        <div class="loading">Loading...</div>
                    </div>
                    <script src="/js/bundle.js"></script>
                </body>
            </html>
        `;
    }
}

// SSR Renderer
class SSRRenderer {
    async render(request) {
        const url = new URL(request.url);
        const pathname = url.pathname;
        
        // Fetch data
        const data = await this.fetchData(pathname);
        
        // Render HTML
        const html = await this.renderHTML(pathname, data);
        
        return new Response(html, {
            headers: {
                'Content-Type': 'text/html',
                'Cache-Control': 'public, max-age=0'
            }
        });
    }
    
    async fetchData(pathname) {
        const response = await fetch(`${process.env.API_URL}${pathname}`);
        return response.json();
    }
    
    async renderHTML(pathname, data) {
        // Render HTML using React or template engine
        return `
            <!DOCTYPE html>
            <html>
                <head>
                    <title>SSR Page</title>
                </head>
                <body>
                    <div id="root">
                        <h1>SSR Content</h1>
                        <div id="data">${JSON.stringify(data)}</div>
                    </div>
                </body>
            </html>
        `;
    }
}

// Usage
const hybridRenderer = new HybridRenderer();

// Handle requests
addEventListener('fetch', event => {
    event.respondWith(hybridRenderer.render(event.request));
});
```

## Exercise: Hybrid Rendering System

Create a comprehensive hybrid rendering system with:
- Multiple rendering strategies
- Route-based strategy selection
- ISR implementation
- Progressive enhancement
- Edge-side rendering
- Performance optimization

## Key Takeaways
- Hybrid rendering combines multiple approaches for optimal performance
- Use ISR for content that changes frequently
- Implement progressive enhancement for better accessibility
- Use edge-side rendering for global performance
- Choose rendering strategy based on content type and requirements
- Monitor performance across different rendering strategies
- Implement proper fallbacks for each strategy
- Test hybrid rendering across different scenarios
- Consider the trade-offs of each rendering approach
- Optimize for both performance and user experience
