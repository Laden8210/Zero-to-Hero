# Server-Side Rendering (SSR)

## Introduction
Server-Side Rendering (SSR) generates HTML on the server and sends it to the browser. This lesson covers SSR implementation, hydration, performance optimization, and best practices.

## SSR Implementation

### Basic SSR Setup
```javascript
// Basic SSR implementation with Express.js
const express = require('express');
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const { StaticRouter } = require('react-router-dom');

const app = express();

// Serve static files
app.use(express.static('public'));

// SSR middleware
app.use('*', (req, res) => {
    const context = {};
    
    // Render React component to string
    const html = ReactDOMServer.renderToString(
        React.createElement(App, {
            location: req.url,
            context: context
        })
    );
    
    // Check for redirects
    if (context.url) {
        return res.redirect(301, context.url);
    }
    
    // Send HTML response
    res.send(`
        <!DOCTYPE html>
        <html>
            <head>
                <title>SSR App</title>
                <meta charset="utf-8">
                <meta name="viewport" content="width=device-width, initial-scale=1">
                <link rel="stylesheet" href="/styles/main.css">
            </head>
            <body>
                <div id="root">${html}</div>
                <script src="/js/bundle.js"></script>
            </body>
        </html>
    `);
});

// App component
function App({ location, context }) {
    return React.createElement(StaticRouter, {
        location: location,
        context: context
    }, React.createElement(Routes));
}

// Routes component
function Routes() {
    return React.createElement('div', { className: 'app' },
        React.createElement('nav', { className: 'navigation' },
            React.createElement('a', { href: '/' }, 'Home'),
            React.createElement('a', { href: '/about' }, 'About'),
            React.createElement('a', { href: '/users' }, 'Users')
        ),
        React.createElement('main', { className: 'main-content' },
            React.createElement(Route, { path: '/', exact: true, component: Home }),
            React.createElement(Route, { path: '/about', component: About }),
            React.createElement(Route, { path: '/users', component: Users })
        )
    );
}

// Page components
function Home() {
    return React.createElement('div', { className: 'home' },
        React.createElement('h1', null, 'Welcome to SSR App'),
        React.createElement('p', null, 'This is a server-side rendered application.')
    );
}

function About() {
    return React.createElement('div', { className: 'about' },
        React.createElement('h1', null, 'About Us'),
        React.createElement('p', null, 'Learn more about our company.')
    );
}

function Users() {
    return React.createElement('div', { className: 'users' },
        React.createElement('h1', null, 'Users'),
        React.createElement('p', null, 'List of users will be loaded here.')
    );
}

// Start server
app.listen(3000, () => {
    console.log('SSR server running on port 3000');
});
```

### Advanced SSR with Data Fetching
```javascript
// Advanced SSR with data fetching
const express = require('express');
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const { StaticRouter } = require('react-router-dom');

const app = express();

// Data fetching utilities
class DataFetcher {
    constructor() {
        this.cache = new Map();
    }
    
    async fetchData(url) {
        if (this.cache.has(url)) {
            return this.cache.get(url);
        }
        
        try {
            const response = await fetch(url);
            const data = await response.json();
            this.cache.set(url, data);
            return data;
        } catch (error) {
            console.error('Failed to fetch data:', error);
            return null;
        }
    }
    
    clearCache() {
        this.cache.clear();
    }
}

const dataFetcher = new DataFetcher();

// SSR middleware with data fetching
app.use('*', async (req, res) => {
    const context = {};
    
    try {
        // Fetch data for the current route
        const routeData = await fetchRouteData(req.url);
        
        // Render React component with data
        const html = ReactDOMServer.renderToString(
            React.createElement(App, {
                location: req.url,
                context: context,
                initialData: routeData
            })
        );
        
        // Check for redirects
        if (context.url) {
            return res.redirect(301, context.url);
        }
        
        // Send HTML response with data
        res.send(`
            <!DOCTYPE html>
            <html>
                <head>
                    <title>SSR App</title>
                    <meta charset="utf-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1">
                    <link rel="stylesheet" href="/styles/main.css">
                </head>
                <body>
                    <div id="root">${html}</div>
                    <script>
                        window.__INITIAL_DATA__ = ${JSON.stringify(routeData)};
                    </script>
                    <script src="/js/bundle.js"></script>
                </body>
            </html>
        `);
    } catch (error) {
        console.error('SSR error:', error);
        res.status(500).send('Internal Server Error');
    }
});

// Fetch data for specific routes
async function fetchRouteData(url) {
    const data = {};
    
    if (url === '/') {
        data.homeData = await dataFetcher.fetchData('/api/home');
    } else if (url === '/about') {
        data.aboutData = await dataFetcher.fetchData('/api/about');
    } else if (url === '/users') {
        data.usersData = await dataFetcher.fetchData('/api/users');
    }
    
    return data;
}

// App component with data
function App({ location, context, initialData }) {
    return React.createElement(StaticRouter, {
        location: location,
        context: context
    }, React.createElement(Routes, { initialData }));
}

// Routes component with data
function Routes({ initialData }) {
    return React.createElement('div', { className: 'app' },
        React.createElement('nav', { className: 'navigation' },
            React.createElement('a', { href: '/' }, 'Home'),
            React.createElement('a', { href: '/about' }, 'About'),
            React.createElement('a', { href: '/users' }, 'Users')
        ),
        React.createElement('main', { className: 'main-content' },
            React.createElement(Route, { 
                path: '/', 
                exact: true, 
                component: Home,
                initialData: initialData
            }),
            React.createElement(Route, { 
                path: '/about', 
                component: About,
                initialData: initialData
            }),
            React.createElement(Route, { 
                path: '/users', 
                component: Users,
                initialData: initialData
            })
        )
    );
}

// Page components with data
function Home({ initialData }) {
    const homeData = initialData?.homeData;
    
    return React.createElement('div', { className: 'home' },
        React.createElement('h1', null, 'Welcome to SSR App'),
        React.createElement('p', null, 'This is a server-side rendered application.'),
        homeData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'Server Data'),
            React.createElement('pre', null, JSON.stringify(homeData, null, 2))
        ) : null
    );
}

function About({ initialData }) {
    const aboutData = initialData?.aboutData;
    
    return React.createElement('div', { className: 'about' },
        React.createElement('h1', null, 'About Us'),
        React.createElement('p', null, 'Learn more about our company.'),
        aboutData ? React.createElement('div', { className: 'data' },
            React.createElement('h3', null, 'About Data'),
            React.createElement('pre', null, JSON.stringify(aboutData, null, 2))
        ) : null
    );
}

function Users({ initialData }) {
    const usersData = initialData?.usersData;
    
    return React.createElement('div', { className: 'users' },
        React.createElement('h1', null, 'Users'),
        usersData ? React.createElement('div', { className: 'users-list' },
            usersData.map(user => 
                React.createElement('div', { key: user.id, className: 'user-card' },
                    React.createElement('h3', null, user.name),
                    React.createElement('p', null, user.email)
                )
            )
        ) : React.createElement('p', null, 'Loading users...')
    );
}

// Start server
app.listen(3000, () => {
    console.log('SSR server running on port 3000');
});
```

## Hydration

### Client-Side Hydration
```javascript
// Client-side hydration
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';

// App component
function App() {
    return (
        <BrowserRouter>
            <Routes />
        </BrowserRouter>
    );
}

// Routes component
function Routes() {
    return (
        <div className="app">
            <nav className="navigation">
                <a href="/">Home</a>
                <a href="/about">About</a>
                <a href="/users">Users</a>
            </nav>
            
            <main className="main-content">
                <Route path="/" exact component={Home} />
                <Route path="/about" component={About} />
                <Route path="/users" component={Users} />
            </main>
        </div>
    );
}

// Page components
function Home() {
    const [data, setData] = useState(null);
    
    useEffect(() => {
        // Use server data if available
        if (window.__INITIAL_DATA__?.homeData) {
            setData(window.__INITIAL_DATA__.homeData);
        } else {
            // Fetch data if not available
            fetchData();
        }
    }, []);
    
    const fetchData = async () => {
        try {
            const response = await fetch('/api/home');
            const result = await response.json();
            setData(result);
        } catch (error) {
            console.error('Failed to fetch data:', error);
        }
    };
    
    return (
        <div className="home">
            <h1>Welcome to SSR App</h1>
            <p>This is a server-side rendered application.</p>
            {data && (
                <div className="data">
                    <h3>Data</h3>
                    <pre>{JSON.stringify(data, null, 2)}</pre>
                </div>
            )}
        </div>
    );
}

function About() {
    const [data, setData] = useState(null);
    
    useEffect(() => {
        if (window.__INITIAL_DATA__?.aboutData) {
            setData(window.__INITIAL_DATA__.aboutData);
        } else {
            fetchData();
        }
    }, []);
    
    const fetchData = async () => {
        try {
            const response = await fetch('/api/about');
            const result = await response.json();
            setData(result);
        } catch (error) {
            console.error('Failed to fetch data:', error);
        }
    };
    
    return (
        <div className="about">
            <h1>About Us</h1>
            <p>Learn more about our company.</p>
            {data && (
                <div className="data">
                    <h3>About Data</h3>
                    <pre>{JSON.stringify(data, null, 2)}</pre>
                </div>
            )}
        </div>
    );
}

function Users() {
    const [users, setUsers] = useState([]);
    
    useEffect(() => {
        if (window.__INITIAL_DATA__?.usersData) {
            setUsers(window.__INITIAL_DATA__.usersData);
        } else {
            fetchUsers();
        }
    }, []);
    
    const fetchUsers = async () => {
        try {
            const response = await fetch('/api/users');
            const result = await response.json();
            setUsers(result);
        } catch (error) {
            console.error('Failed to fetch users:', error);
        }
    };
    
    return (
        <div className="users">
            <h1>Users</h1>
            <div className="users-list">
                {users.map(user => (
                    <div key={user.id} className="user-card">
                        <h3>{user.name}</h3>
                        <p>{user.email}</p>
                    </div>
                ))}
            </div>
        </div>
    );
}

// Hydrate the app
ReactDOM.hydrate(<App />, document.getElementById('root'));

// Clear initial data after hydration
delete window.__INITIAL_DATA__;
```

### Advanced Hydration Patterns
```javascript
// Advanced hydration with error boundaries
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

// Error boundary for hydration
class HydrationErrorBoundary extends Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null };
    }
    
    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }
    
    componentDidCatch(error, errorInfo) {
        console.error('Hydration error:', error, errorInfo);
        
        // Report error to monitoring service
        if (window.Sentry) {
            window.Sentry.captureException(error, {
                contexts: {
                    react: {
                        componentStack: errorInfo.componentStack
                    }
                }
            });
        }
    }
    
    render() {
        if (this.state.hasError) {
            return (
                <div className="error-boundary">
                    <h2>Something went wrong during hydration</h2>
                    <p>Please refresh the page to try again.</p>
                    <button onClick={() => window.location.reload()}>
                        Refresh Page
                    </button>
                </div>
            );
        }
        
        return this.props.children;
    }
}

// Hydration manager
class HydrationManager {
    constructor() {
        this.isHydrated = false;
        this.hydrationCallbacks = [];
    }
    
    onHydrated(callback) {
        if (this.isHydrated) {
            callback();
        } else {
            this.hydrationCallbacks.push(callback);
        }
    }
    
    markAsHydrated() {
        this.isHydrated = true;
        this.hydrationCallbacks.forEach(callback => callback());
        this.hydrationCallbacks = [];
    }
}

const hydrationManager = new HydrationManager();

// App component with hydration management
function App() {
    const [isHydrated, setIsHydrated] = useState(false);
    
    useEffect(() => {
        // Mark as hydrated after component mounts
        hydrationManager.markAsHydrated();
        setIsHydrated(true);
    }, []);
    
    return (
        <HydrationErrorBoundary>
            <BrowserRouter>
                <Routes isHydrated={isHydrated} />
            </BrowserRouter>
        </HydrationErrorBoundary>
    );
}

// Routes component with hydration awareness
function Routes({ isHydrated }) {
    return (
        <div className="app">
            <nav className="navigation">
                <a href="/">Home</a>
                <a href="/about">About</a>
                <a href="/users">Users</a>
            </nav>
            
            <main className="main-content">
                <Route path="/" exact component={Home} />
                <Route path="/about" component={About} />
                <Route path="/users" component={Users} />
            </main>
            
            {!isHydrated && (
                <div className="hydration-indicator">
                    Hydrating...
                </div>
            )}
        </div>
    );
}

// Hydrate with error handling
try {
    ReactDOM.hydrate(<App />, document.getElementById('root'));
} catch (error) {
    console.error('Hydration failed:', error);
    
    // Fallback to client-side rendering
    ReactDOM.render(<App />, document.getElementById('root'));
}

// Clear initial data after hydration
delete window.__INITIAL_DATA__;
```

## SSR Performance Optimization

### Caching and Performance
```javascript
// SSR performance optimization
const express = require('express');
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const { StaticRouter } = require('react-router-dom');

const app = express();

// Caching
const cache = new Map();
const CACHE_TTL = 5 * 60 * 1000; // 5 minutes

// Cache middleware
function cacheMiddleware(req, res, next) {
    const key = req.url;
    const cached = cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
        return res.send(cached.html);
    }
    
    req.cacheKey = key;
    next();
}

// SSR middleware with caching
app.use('*', cacheMiddleware, async (req, res) => {
    const context = {};
    
    try {
        // Fetch data for the current route
        const routeData = await fetchRouteData(req.url);
        
        // Render React component with data
        const html = ReactDOMServer.renderToString(
            React.createElement(App, {
                location: req.url,
                context: context,
                initialData: routeData
            })
        );
        
        // Check for redirects
        if (context.url) {
            return res.redirect(301, context.url);
        }
        
        // Generate full HTML
        const fullHtml = `
            <!DOCTYPE html>
            <html>
                <head>
                    <title>SSR App</title>
                    <meta charset="utf-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1">
                    <link rel="stylesheet" href="/styles/main.css">
                </head>
                <body>
                    <div id="root">${html}</div>
                    <script>
                        window.__INITIAL_DATA__ = ${JSON.stringify(routeData)};
                    </script>
                    <script src="/js/bundle.js"></script>
                </body>
            </html>
        `;
        
        // Cache the response
        cache.set(req.cacheKey, {
            html: fullHtml,
            timestamp: Date.now()
        });
        
        // Send HTML response
        res.send(fullHtml);
    } catch (error) {
        console.error('SSR error:', error);
        res.status(500).send('Internal Server Error');
    }
});

// Data fetching with caching
const dataCache = new Map();
const DATA_CACHE_TTL = 2 * 60 * 1000; // 2 minutes

async function fetchRouteData(url) {
    const data = {};
    
    if (url === '/') {
        data.homeData = await fetchDataWithCache('/api/home');
    } else if (url === '/about') {
        data.aboutData = await fetchDataWithCache('/api/about');
    } else if (url === '/users') {
        data.usersData = await fetchDataWithCache('/api/users');
    }
    
    return data;
}

async function fetchDataWithCache(url) {
    const cached = dataCache.get(url);
    
    if (cached && Date.now() - cached.timestamp < DATA_CACHE_TTL) {
        return cached.data;
    }
    
    try {
        const response = await fetch(url);
        const data = await response.json();
        
        dataCache.set(url, {
            data,
            timestamp: Date.now()
        });
        
        return data;
    } catch (error) {
        console.error('Failed to fetch data:', error);
        return null;
    }
}

// Cache cleanup
setInterval(() => {
    const now = Date.now();
    
    // Clean up expired cache entries
    for (const [key, value] of cache.entries()) {
        if (now - value.timestamp > CACHE_TTL) {
            cache.delete(key);
        }
    }
    
    // Clean up expired data cache entries
    for (const [key, value] of dataCache.entries()) {
        if (now - value.timestamp > DATA_CACHE_TTL) {
            dataCache.delete(key);
        }
    }
}, 60000); // Clean up every minute

// Start server
app.listen(3000, () => {
    console.log('SSR server running on port 3000');
});
```

## Exercise: SSR Application

Create a comprehensive SSR application with:
- Basic SSR implementation
- Data fetching and caching
- Client-side hydration
- Error boundaries
- Performance optimization
- Caching strategies

## Key Takeaways
- SSR provides better SEO and initial page load performance
- Use data fetching to populate initial state
- Implement proper hydration for client-side interactivity
- Use caching to improve SSR performance
- Handle hydration errors gracefully
- Monitor SSR performance and optimize accordingly
- Use error boundaries for better error handling
- Test SSR applications across different scenarios
- Consider the trade-offs between SSR and CSR
- Implement proper caching strategies for data and HTML
