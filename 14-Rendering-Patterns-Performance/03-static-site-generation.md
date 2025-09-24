# Static Site Generation (SSG)

## Introduction
Static Site Generation (SSG) pre-builds pages at build time, creating static HTML files. This lesson covers SSG implementation, build-time data fetching, and optimization techniques.

## SSG Implementation

### Basic SSG Setup
```javascript
// Basic SSG implementation
const fs = require('fs');
const path = require('path');
const React = require('react');
const ReactDOMServer = require('react-dom/server');

// Build configuration
const BUILD_DIR = 'dist';
const PAGES_DIR = 'src/pages';

// Page configuration
const pages = [
    { path: '/', component: 'Home', template: 'index.html' },
    { path: '/about', component: 'About', template: 'about.html' },
    { path: '/contact', component: 'Contact', template: 'contact.html' },
    { path: '/blog', component: 'Blog', template: 'blog.html' }
];

// Build function
async function build() {
    console.log('Starting SSG build...');
    
    // Create build directory
    if (!fs.existsSync(BUILD_DIR)) {
        fs.mkdirSync(BUILD_DIR, { recursive: true });
    }
    
    // Build each page
    for (const page of pages) {
        await buildPage(page);
    }
    
    // Copy static assets
    copyStaticAssets();
    
    console.log('SSG build completed!');
}

// Build individual page
async function buildPage(pageConfig) {
    const { path: pagePath, component: componentName, template } = pageConfig;
    
    try {
        // Load component
        const Component = require(`./${PAGES_DIR}/${componentName}`);
        
        // Fetch data for the page
        const data = await fetchPageData(pagePath);
        
        // Render component to HTML
        const html = ReactDOMServer.renderToString(
            React.createElement(Component, { data })
        );
        
        // Generate full HTML
        const fullHtml = generateHTML(html, data, template);
        
        // Write to file
        const outputPath = path.join(BUILD_DIR, pagePath === '/' ? 'index.html' : `${pagePath}/index.html`);
        
        // Create directory if it doesn't exist
        const outputDir = path.dirname(outputPath);
        if (!fs.existsSync(outputDir)) {
            fs.mkdirSync(outputDir, { recursive: true });
        }
        
        fs.writeFileSync(outputPath, fullHtml);
        
        console.log(`Built page: ${pagePath}`);
    } catch (error) {
        console.error(`Failed to build page ${pagePath}:`, error);
    }
}

// Fetch data for specific pages
async function fetchPageData(pagePath) {
    const data = {};
    
    switch (pagePath) {
        case '/':
            data.homeData = await fetchData('/api/home');
            break;
        case '/about':
            data.aboutData = await fetchData('/api/about');
            break;
        case '/contact':
            data.contactData = await fetchData('/api/contact');
            break;
        case '/blog':
            data.blogData = await fetchData('/api/blog');
            break;
    }
    
    return data;
}

// Fetch data from API
async function fetchData(url) {
    try {
        const response = await fetch(url);
        return await response.json();
    } catch (error) {
        console.error(`Failed to fetch data from ${url}:`, error);
        return null;
    }
}

// Generate HTML template
function generateHTML(content, data, template) {
    return `
        <!DOCTYPE html>
        <html lang="en">
            <head>
                <meta charset="UTF-8">
                <meta name="viewport" content="width=device-width, initial-scale=1.0">
                <title>SSG App</title>
                <link rel="stylesheet" href="/styles/main.css">
            </head>
            <body>
                <div id="root">${content}</div>
                <script>
                    window.__INITIAL_DATA__ = ${JSON.stringify(data)};
                </script>
                <script src="/js/bundle.js"></script>
            </body>
        </html>
    `;
}

// Copy static assets
function copyStaticAssets() {
    const staticDir = 'public';
    const staticFiles = fs.readdirSync(staticDir);
    
    staticFiles.forEach(file => {
        const srcPath = path.join(staticDir, file);
        const destPath = path.join(BUILD_DIR, file);
        
        if (fs.statSync(srcPath).isDirectory()) {
            copyDirectory(srcPath, destPath);
        } else {
            fs.copyFileSync(srcPath, destPath);
        }
    });
}

// Copy directory recursively
function copyDirectory(src, dest) {
    if (!fs.existsSync(dest)) {
        fs.mkdirSync(dest, { recursive: true });
    }
    
    const files = fs.readdirSync(src);
    
    files.forEach(file => {
        const srcPath = path.join(src, file);
        const destPath = path.join(dest, file);
        
        if (fs.statSync(srcPath).isDirectory()) {
            copyDirectory(srcPath, destPath);
        } else {
            fs.copyFileSync(srcPath, destPath);
        }
    });
}

// Page components
function Home({ data }) {
    const homeData = data?.homeData;
    
    return React.createElement('div', { className: 'home' },
        React.createElement('h1', null, 'Welcome to SSG App'),
        React.createElement('p', null, 'This is a statically generated site.'),
        homeData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'Static Data'),
            React.createElement('pre', null, JSON.stringify(homeData, null, 2))
        ) : null
    );
}

function About({ data }) {
    const aboutData = data?.aboutData;
    
    return React.createElement('div', { className: 'about' },
        React.createElement('h1', null, 'About Us'),
        React.createElement('p', null, 'Learn more about our company.'),
        aboutData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'About Data'),
            React.createElement('pre', null, JSON.stringify(aboutData, null, 2))
        ) : null
    );
}

function Contact({ data }) {
    const contactData = data?.contactData;
    
    return React.createElement('div', { className: 'contact' },
        React.createElement('h1', null, 'Contact Us'),
        React.createElement('p', null, 'Get in touch with us.'),
        contactData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'Contact Data'),
            React.createElement('pre', null, JSON.stringify(contactData, null, 2))
        ) : null
    );
}

function Blog({ data }) {
    const blogData = data?.blogData;
    
    return React.createElement('div', { className: 'blog' },
        React.createElement('h1', null, 'Blog'),
        React.createElement('p', null, 'Read our latest posts.'),
        blogData ? React.createElement('div', { className: 'posts' },
            blogData.map(post => 
                React.createElement('div', { key: post.id, className: 'post' },
                    React.createElement('h3', null, post.title),
                    React.createElement('p', null, post.excerpt)
                )
            )
        ) : null
    );
}

// Export components
module.exports = {
    Home,
    About,
    Contact,
    Blog
};

// Run build
if (require.main === module) {
    build();
}
```

### Advanced SSG with Dynamic Routes
```javascript
// Advanced SSG with dynamic routes
const fs = require('fs');
const path = require('path');
const React = require('react');
const ReactDOMServer = require('react-dom/server');

// Build configuration
const BUILD_DIR = 'dist';
const PAGES_DIR = 'src/pages';

// Dynamic page configuration
const dynamicPages = [
    {
        path: '/blog',
        component: 'Blog',
        template: 'blog.html',
        dynamic: true,
        dataSource: '/api/blog'
    },
    {
        path: '/users',
        component: 'Users',
        template: 'users.html',
        dynamic: true,
        dataSource: '/api/users'
    }
];

// Build function with dynamic routes
async function build() {
    console.log('Starting SSG build with dynamic routes...');
    
    // Create build directory
    if (!fs.existsSync(BUILD_DIR)) {
        fs.mkdirSync(BUILD_DIR, { recursive: true });
    }
    
    // Build static pages
    await buildStaticPages();
    
    // Build dynamic pages
    await buildDynamicPages();
    
    // Copy static assets
    copyStaticAssets();
    
    console.log('SSG build completed!');
}

// Build static pages
async function buildStaticPages() {
    const staticPages = [
        { path: '/', component: 'Home', template: 'index.html' },
        { path: '/about', component: 'About', template: 'about.html' },
        { path: '/contact', component: 'Contact', template: 'contact.html' }
    ];
    
    for (const page of staticPages) {
        await buildPage(page);
    }
}

// Build dynamic pages
async function buildDynamicPages() {
    for (const page of dynamicPages) {
        await buildDynamicPage(page);
    }
}

// Build dynamic page
async function buildDynamicPage(pageConfig) {
    const { path: pagePath, component: componentName, template, dataSource } = pageConfig;
    
    try {
        // Load component
        const Component = require(`./${PAGES_DIR}/${componentName}`);
        
        // Fetch data for dynamic routes
        const data = await fetchDynamicData(dataSource);
        
        if (Array.isArray(data)) {
            // Build individual pages for each data item
            for (const item of data) {
                await buildDynamicPageItem(pageConfig, Component, item);
            }
        } else {
            // Build single page
            await buildDynamicPageItem(pageConfig, Component, data);
        }
    } catch (error) {
        console.error(`Failed to build dynamic page ${pagePath}:`, error);
    }
}

// Build individual dynamic page item
async function buildDynamicPageItem(pageConfig, Component, item) {
    const { path: pagePath, template } = pageConfig;
    
    try {
        // Generate page path for individual item
        const itemPath = `${pagePath}/${item.slug || item.id}`;
        
        // Render component with item data
        const html = ReactDOMServer.renderToString(
            React.createElement(Component, { data: item })
        );
        
        // Generate full HTML
        const fullHtml = generateHTML(html, item, template);
        
        // Write to file
        const outputPath = path.join(BUILD_DIR, `${itemPath}/index.html`);
        
        // Create directory if it doesn't exist
        const outputDir = path.dirname(outputPath);
        if (!fs.existsSync(outputDir)) {
            fs.mkdirSync(outputDir, { recursive: true });
        }
        
        fs.writeFileSync(outputPath, fullHtml);
        
        console.log(`Built dynamic page: ${itemPath}`);
    } catch (error) {
        console.error(`Failed to build dynamic page item:`, error);
    }
}

// Fetch data for dynamic routes
async function fetchDynamicData(dataSource) {
    try {
        const response = await fetch(dataSource);
        return await response.json();
    } catch (error) {
        console.error(`Failed to fetch dynamic data from ${dataSource}:`, error);
        return [];
    }
}

// Build individual page
async function buildPage(pageConfig) {
    const { path: pagePath, component: componentName, template } = pageConfig;
    
    try {
        // Load component
        const Component = require(`./${PAGES_DIR}/${componentName}`);
        
        // Fetch data for the page
        const data = await fetchPageData(pagePath);
        
        // Render component to HTML
        const html = ReactDOMServer.renderToString(
            React.createElement(Component, { data })
        );
        
        // Generate full HTML
        const fullHtml = generateHTML(html, data, template);
        
        // Write to file
        const outputPath = path.join(BUILD_DIR, pagePath === '/' ? 'index.html' : `${pagePath}/index.html`);
        
        // Create directory if it doesn't exist
        const outputDir = path.dirname(outputPath);
        if (!fs.existsSync(outputDir)) {
            fs.mkdirSync(outputDir, { recursive: true });
        }
        
        fs.writeFileSync(outputPath, fullHtml);
        
        console.log(`Built page: ${pagePath}`);
    } catch (error) {
        console.error(`Failed to build page ${pagePath}:`, error);
    }
}

// Fetch data for specific pages
async function fetchPageData(pagePath) {
    const data = {};
    
    switch (pagePath) {
        case '/':
            data.homeData = await fetchData('/api/home');
            break;
        case '/about':
            data.aboutData = await fetchData('/api/about');
            break;
        case '/contact':
            data.contactData = await fetchData('/api/contact');
            break;
    }
    
    return data;
}

// Fetch data from API
async function fetchData(url) {
    try {
        const response = await fetch(url);
        return await response.json();
    } catch (error) {
        console.error(`Failed to fetch data from ${url}:`, error);
        return null;
    }
}

// Generate HTML template
function generateHTML(content, data, template) {
    return `
        <!DOCTYPE html>
        <html lang="en">
            <head>
                <meta charset="UTF-8">
                <meta name="viewport" content="width=device-width, initial-scale=1.0">
                <title>SSG App</title>
                <link rel="stylesheet" href="/styles/main.css">
            </head>
            <body>
                <div id="root">${content}</div>
                <script>
                    window.__INITIAL_DATA__ = ${JSON.stringify(data)};
                </script>
                <script src="/js/bundle.js"></script>
            </body>
        </html>
    `;
}

// Copy static assets
function copyStaticAssets() {
    const staticDir = 'public';
    const staticFiles = fs.readdirSync(staticDir);
    
    staticFiles.forEach(file => {
        const srcPath = path.join(staticDir, file);
        const destPath = path.join(BUILD_DIR, file);
        
        if (fs.statSync(srcPath).isDirectory()) {
            copyDirectory(srcPath, destPath);
        } else {
            fs.copyFileSync(srcPath, destPath);
        }
    });
}

// Copy directory recursively
function copyDirectory(src, dest) {
    if (!fs.existsSync(dest)) {
        fs.mkdirSync(dest, { recursive: true });
    }
    
    const files = fs.readdirSync(src);
    
    files.forEach(file => {
        const srcPath = path.join(src, file);
        const destPath = path.join(dest, file);
        
        if (fs.statSync(srcPath).isDirectory()) {
            copyDirectory(srcPath, destPath);
        } else {
            fs.copyFileSync(srcPath, destPath);
        }
    });
}

// Page components
function Home({ data }) {
    const homeData = data?.homeData;
    
    return React.createElement('div', { className: 'home' },
        React.createElement('h1', null, 'Welcome to SSG App'),
        React.createElement('p', null, 'This is a statically generated site.'),
        homeData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'Static Data'),
            React.createElement('pre', null, JSON.stringify(homeData, null, 2))
        ) : null
    );
}

function About({ data }) {
    const aboutData = data?.aboutData;
    
    return React.createElement('div', { className: 'about' },
        React.createElement('h1', null, 'About Us'),
        React.createElement('p', null, 'Learn more about our company.'),
        aboutData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'About Data'),
            React.createElement('pre', null, JSON.stringify(aboutData, null, 2))
        ) : null
    );
}

function Contact({ data }) {
    const contactData = data?.contactData;
    
    return React.createElement('div', { className: 'contact' },
        React.createElement('h1', null, 'Contact Us'),
        React.createElement('p', null, 'Get in touch with us.'),
        contactData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'Contact Data'),
            React.createElement('pre', null, JSON.stringify(contactData, null, 2))
        ) : null
    );
}

function Blog({ data }) {
    const post = data;
    
    return React.createElement('div', { className: 'blog-post' },
        React.createElement('h1', null, post.title),
        React.createElement('div', { className: 'meta' },
            React.createElement('span', { className: 'date' }, post.date),
            React.createElement('span', { className: 'author' }, post.author)
        ),
        React.createElement('div', { className: 'content' },
            React.createElement('p', null, post.content)
        )
    );
}

function Users({ data }) {
    const user = data;
    
    return React.createElement('div', { className: 'user-profile' },
        React.createElement('h1', null, user.name),
        React.createElement('div', { className: 'user-info' },
            React.createElement('p', null, `Email: ${user.email}`),
            React.createElement('p', null, `Role: ${user.role}`)
        )
    );
}

// Export components
module.exports = {
    Home,
    About,
    Contact,
    Blog,
    Users
};

// Run build
if (require.main === module) {
    build();
}
```

## SSG Optimization

### Build Optimization
```javascript
// SSG build optimization
const fs = require('fs');
const path = require('path');
const React = require('react');
const ReactDOMServer = require('react-dom/server');

// Build configuration
const BUILD_DIR = 'dist';
const PAGES_DIR = 'src/pages';

// Build optimization
class SSGBuilder {
    constructor() {
        this.cache = new Map();
        this.buildStats = {
            startTime: Date.now(),
            pagesBuilt: 0,
            errors: []
        };
    }
    
    async build() {
        console.log('Starting optimized SSG build...');
        
        // Create build directory
        this.ensureBuildDir();
        
        // Build pages in parallel
        await this.buildPagesInParallel();
        
        // Copy static assets
        this.copyStaticAssets();
        
        // Generate build report
        this.generateBuildReport();
        
        console.log('SSG build completed!');
    }
    
    ensureBuildDir() {
        if (!fs.existsSync(BUILD_DIR)) {
            fs.mkdirSync(BUILD_DIR, { recursive: true });
        }
    }
    
    async buildPagesInParallel() {
        const pages = [
            { path: '/', component: 'Home', template: 'index.html' },
            { path: '/about', component: 'About', template: 'about.html' },
            { path: '/contact', component: 'Contact', template: 'contact.html' },
            { path: '/blog', component: 'Blog', template: 'blog.html' }
        ];
        
        // Build pages in parallel with concurrency limit
        const concurrency = 3;
        const chunks = this.chunkArray(pages, concurrency);
        
        for (const chunk of chunks) {
            await Promise.all(chunk.map(page => this.buildPage(page)));
        }
    }
    
    chunkArray(array, size) {
        const chunks = [];
        for (let i = 0; i < array.length; i += size) {
            chunks.push(array.slice(i, i + size));
        }
        return chunks;
    }
    
    async buildPage(pageConfig) {
        const { path: pagePath, component: componentName, template } = pageConfig;
        
        try {
            // Check cache first
            const cacheKey = `${pagePath}-${componentName}`;
            if (this.cache.has(cacheKey)) {
                const cached = this.cache.get(cacheKey);
                if (Date.now() - cached.timestamp < 60000) { // 1 minute cache
                    return cached.html;
                }
            }
            
            // Load component
            const Component = require(`./${PAGES_DIR}/${componentName}`);
            
            // Fetch data for the page
            const data = await this.fetchPageData(pagePath);
            
            // Render component to HTML
            const html = ReactDOMServer.renderToString(
                React.createElement(Component, { data })
            );
            
            // Generate full HTML
            const fullHtml = this.generateHTML(html, data, template);
            
            // Write to file
            const outputPath = path.join(BUILD_DIR, pagePath === '/' ? 'index.html' : `${pagePath}/index.html`);
            
            // Create directory if it doesn't exist
            const outputDir = path.dirname(outputPath);
            if (!fs.existsSync(outputDir)) {
                fs.mkdirSync(outputDir, { recursive: true });
            }
            
            fs.writeFileSync(outputPath, fullHtml);
            
            // Cache the result
            this.cache.set(cacheKey, {
                html: fullHtml,
                timestamp: Date.now()
            });
            
            this.buildStats.pagesBuilt++;
            console.log(`Built page: ${pagePath}`);
        } catch (error) {
            console.error(`Failed to build page ${pagePath}:`, error);
            this.buildStats.errors.push({
                page: pagePath,
                error: error.message
            });
        }
    }
    
    async fetchPageData(pagePath) {
        const data = {};
        
        switch (pagePath) {
            case '/':
                data.homeData = await this.fetchData('/api/home');
                break;
            case '/about':
                data.aboutData = await this.fetchData('/api/about');
                break;
            case '/contact':
                data.contactData = await this.fetchData('/api/contact');
                break;
            case '/blog':
                data.blogData = await this.fetchData('/api/blog');
                break;
        }
        
        return data;
    }
    
    async fetchData(url) {
        try {
            const response = await fetch(url);
            return await response.json();
        } catch (error) {
            console.error(`Failed to fetch data from ${url}:`, error);
            return null;
        }
    }
    
    generateHTML(content, data, template) {
        return `
            <!DOCTYPE html>
            <html lang="en">
                <head>
                    <meta charset="UTF-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1.0">
                    <title>SSG App</title>
                    <link rel="stylesheet" href="/styles/main.css">
                </head>
                <body>
                    <div id="root">${content}</div>
                    <script>
                        window.__INITIAL_DATA__ = ${JSON.stringify(data)};
                    </script>
                    <script src="/js/bundle.js"></script>
                </body>
            </html>
        `;
    }
    
    copyStaticAssets() {
        const staticDir = 'public';
        const staticFiles = fs.readdirSync(staticDir);
        
        staticFiles.forEach(file => {
            const srcPath = path.join(staticDir, file);
            const destPath = path.join(BUILD_DIR, file);
            
            if (fs.statSync(srcPath).isDirectory()) {
                this.copyDirectory(srcPath, destPath);
            } else {
                fs.copyFileSync(srcPath, destPath);
            }
        });
    }
    
    copyDirectory(src, dest) {
        if (!fs.existsSync(dest)) {
            fs.mkdirSync(dest, { recursive: true });
        }
        
        const files = fs.readdirSync(src);
        
        files.forEach(file => {
            const srcPath = path.join(src, file);
            const destPath = path.join(dest, file);
            
            if (fs.statSync(srcPath).isDirectory()) {
                this.copyDirectory(srcPath, destPath);
            } else {
                fs.copyFileSync(srcPath, destPath);
            }
        });
    }
    
    generateBuildReport() {
        const endTime = Date.now();
        const buildTime = endTime - this.buildStats.startTime;
        
        const report = {
            buildTime,
            pagesBuilt: this.buildStats.pagesBuilt,
            errors: this.buildStats.errors,
            cacheSize: this.cache.size
        };
        
        fs.writeFileSync(
            path.join(BUILD_DIR, 'build-report.json'),
            JSON.stringify(report, null, 2)
        );
        
        console.log('Build Report:', report);
    }
}

// Run optimized build
const builder = new SSGBuilder();
builder.build();
```

## Exercise: SSG Application

Create a comprehensive SSG application with:
- Basic SSG implementation
- Dynamic routes and data fetching
- Build optimization
- Caching strategies
- Build reporting
- Performance monitoring

## Key Takeaways
- SSG provides excellent performance and SEO benefits
- Use build-time data fetching for static content
- Implement dynamic routes for content-driven sites
- Optimize build process with parallel processing
- Use caching to improve build performance
- Monitor build performance and errors
- Generate build reports for analysis
- Test SSG applications across different scenarios
- Consider the trade-offs between SSG and other rendering methods
- Implement proper error handling and fallbacks
