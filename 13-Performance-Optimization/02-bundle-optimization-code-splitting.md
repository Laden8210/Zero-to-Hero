# Bundle Optimization and Code Splitting

## Introduction
Bundle optimization and code splitting are essential for creating fast, efficient web applications. This lesson covers bundle analysis, code splitting strategies, tree shaking, and optimization techniques.

## Bundle Analysis

### Webpack Bundle Analyzer
```javascript
// webpack.config.js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
    // ... other webpack config
    
    plugins: [
        // Only enable in production or when needed
        process.env.ANALYZE && new BundleAnalyzerPlugin({
            analyzerMode: 'static',
            openAnalyzer: false,
            reportFilename: 'bundle-report.html'
        })
    ].filter(Boolean),
    
    optimization: {
        splitChunks: {
            chunks: 'all',
            cacheGroups: {
                vendor: {
                    test: /[\\/]node_modules[\\/]/,
                    name: 'vendors',
                    chunks: 'all',
                },
                common: {
                    name: 'common',
                    minChunks: 2,
                    chunks: 'all',
                    enforce: true
                }
            }
        }
    }
};
```

### Bundle Size Monitoring
```javascript
// Bundle size monitoring script
class BundleSizeMonitor {
    constructor() {
        this.bundleSizes = {};
        this.setupMonitoring();
    }
    
    setupMonitoring() {
        // Monitor bundle sizes in development
        if (process.env.NODE_ENV === 'development') {
            this.monitorBundleSizes();
        }
        
        // Monitor runtime bundle sizes
        this.monitorRuntimeBundles();
    }
    
    monitorBundleSizes() {
        // This would typically be integrated with webpack
        // For demonstration, we'll simulate bundle size monitoring
        const bundles = {
            'main.js': 150000,
            'vendor.js': 300000,
            'styles.css': 50000
        };
        
        Object.entries(bundles).forEach(([name, size]) => {
            this.bundleSizes[name] = size;
            
            // Warn if bundle exceeds size limit
            if (size > 200000) {
                console.warn(`Bundle ${name} exceeds size limit: ${size} bytes`);
            }
        });
    }
    
    monitorRuntimeBundles() {
        // Monitor dynamically loaded chunks
        const observer = new PerformanceObserver((list) => {
            const entries = list.getEntries();
            
            entries.forEach((entry) => {
                if (entry.entryType === 'resource') {
                    const size = entry.transferSize || entry.decodedBodySize;
                    
                    if (entry.name.includes('.js') || entry.name.includes('.css')) {
                        this.bundleSizes[entry.name] = size;
                        
                        // Report large bundles
                        if (size > 100000) {
                            console.warn(`Large bundle loaded: ${entry.name} (${size} bytes)`);
                        }
                    }
                }
            });
        });
        
        observer.observe({ entryTypes: ['resource'] });
    }
    
    getBundleSizes() {
        return this.bundleSizes;
    }
    
    reportBundleSizes() {
        const totalSize = Object.values(this.bundleSizes).reduce((sum, size) => sum + size, 0);
        
        console.log('Bundle Size Report:');
        console.log(`Total size: ${totalSize} bytes (${(totalSize / 1024).toFixed(2)} KB)`);
        
        Object.entries(this.bundleSizes).forEach(([name, size]) => {
            console.log(`${name}: ${size} bytes (${(size / 1024).toFixed(2)} KB)`);
        });
    }
}

// Usage
const bundleMonitor = new BundleSizeMonitor();

// Report bundle sizes after page load
window.addEventListener('load', () => {
    setTimeout(() => {
        bundleMonitor.reportBundleSizes();
    }, 2000);
});
```

## Code Splitting Strategies

### Route-Based Code Splitting
```javascript
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

// Lazy load components
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./pages/Contact'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

// Loading component
function LoadingSpinner() {
    return (
        <div className="loading-spinner">
            <div className="spinner"></div>
            <p>Loading...</p>
        </div>
    );
}

// Error boundary for lazy components
class LazyErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false };
    }
    
    static getDerivedStateFromError(error) {
        return { hasError: true };
    }
    
    componentDidCatch(error, errorInfo) {
        console.error('Lazy component error:', error, errorInfo);
    }
    
    render() {
        if (this.state.hasError) {
            return (
                <div className="error-fallback">
                    <h2>Something went wrong loading this page.</h2>
                    <button onClick={() => this.setState({ hasError: false })}>
                        Try again
                    </button>
                </div>
            );
        }
        
        return this.props.children;
    }
}

// Main App component
function App() {
    return (
        <Router>
            <div className="app">
                <nav>
                    <a href="/">Home</a>
                    <a href="/about">About</a>
                    <a href="/contact">Contact</a>
                    <a href="/dashboard">Dashboard</a>
                    <a href="/profile">Profile</a>
                </nav>
                
                <main>
                    <Suspense fallback={<LoadingSpinner />}>
                        <LazyErrorBoundary>
                            <Routes>
                                <Route path="/" element={<Home />} />
                                <Route path="/about" element={<About />} />
                                <Route path="/contact" element={<Contact />} />
                                <Route path="/dashboard" element={<Dashboard />} />
                                <Route path="/profile" element={<Profile />} />
                            </Routes>
                        </LazyErrorBoundary>
                    </Suspense>
                </main>
            </div>
        </Router>
    );
}

export default App;
```

### Component-Based Code Splitting
```javascript
import React, { Suspense, lazy, useState } from 'react';

// Lazy load components
const Modal = lazy(() => import('./components/Modal'));
const Chart = lazy(() => import('./components/Chart'));
const DataTable = lazy(() => import('./components/DataTable'));

// Component with conditional lazy loading
function Dashboard() {
    const [showModal, setShowModal] = useState(false);
    const [showChart, setShowChart] = useState(false);
    const [showTable, setShowTable] = useState(false);
    
    return (
        <div className="dashboard">
            <h1>Dashboard</h1>
            
            <div className="controls">
                <button onClick={() => setShowModal(true)}>
                    Open Modal
                </button>
                <button onClick={() => setShowChart(!showChart)}>
                    Toggle Chart
                </button>
                <button onClick={() => setShowTable(!showTable)}>
                    Toggle Table
                </button>
            </div>
            
            {showModal && (
                <Suspense fallback={<div>Loading modal...</div>}>
                    <Modal onClose={() => setShowModal(false)}>
                        <h2>Modal Content</h2>
                        <p>This is a lazy-loaded modal.</p>
                    </Modal>
                </Suspense>
            )}
            
            {showChart && (
                <Suspense fallback={<div>Loading chart...</div>}>
                    <Chart data={chartData} />
                </Suspense>
            )}
            
            {showTable && (
                <Suspense fallback={<div>Loading table...</div>}>
                    <DataTable data={tableData} />
                </Suspense>
            )}
        </div>
    );
}

// Dynamic import with error handling
function DynamicComponent({ componentName, ...props }) {
    const [Component, setComponent] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        setLoading(true);
        setError(null);
        
        import(`./components/${componentName}`)
            .then(module => {
                setComponent(() => module.default);
                setLoading(false);
            })
            .catch(err => {
                setError(err);
                setLoading(false);
            });
    }, [componentName]);
    
    if (loading) return <div>Loading {componentName}...</div>;
    if (error) return <div>Error loading {componentName}: {error.message}</div>;
    if (!Component) return <div>Component not found</div>;
    
    return <Component {...props} />;
}

// Usage
function App() {
    return (
        <div>
            <Dashboard />
            <DynamicComponent componentName="Button" text="Click me" />
        </div>
    );
}
```

## Tree Shaking and Dead Code Elimination

### Tree Shaking Configuration
```javascript
// webpack.config.js
module.exports = {
    mode: 'production',
    
    optimization: {
        usedExports: true,
        sideEffects: false,
        minimize: true,
        minimizer: [
            new TerserPlugin({
                terserOptions: {
                    compress: {
                        drop_console: true,
                        drop_debugger: true,
                        pure_funcs: ['console.log']
                    }
                }
            })
        ]
    },
    
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            ['@babel/preset-env', {
                                modules: false // Important for tree shaking
                            }]
                        ]
                    }
                }
            }
        ]
    }
};
```

### Tree Shaking Best Practices
```javascript
// Good: Named exports for tree shaking
export const utils = {
    formatDate: (date) => new Intl.DateTimeFormat('en-US').format(date),
    formatCurrency: (amount) => new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
    }).format(amount),
    debounce: (func, wait) => {
        let timeout;
        return function executedFunction(...args) {
            const later = () => {
                clearTimeout(timeout);
                func(...args);
            };
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
        };
    }
};

// Good: Individual exports
export const formatDate = (date) => new Intl.DateTimeFormat('en-US').format(date);
export const formatCurrency = (amount) => new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD'
}).format(amount);
export const debounce = (func, wait) => {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
};

// Bad: Default export with everything
export default {
    formatDate: (date) => new Intl.DateTimeFormat('en-US').format(date),
    formatCurrency: (amount) => new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
    }).format(amount),
    debounce: (func, wait) => {
        let timeout;
        return function executedFunction(...args) {
            const later = () => {
                clearTimeout(timeout);
                func(...args);
            };
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
        };
    }
};

// Good: Import only what you need
import { formatDate, debounce } from './utils';

// Bad: Import everything
import utils from './utils';

// Good: Conditional imports
if (process.env.NODE_ENV === 'development') {
    import('./dev-tools').then(module => {
        module.initDevTools();
    });
}

// Good: Side effect free imports
import 'normalize.css'; // CSS imports are side effects
import './styles.css'; // CSS imports are side effects

// Bad: Side effect imports
import './polyfills'; // This might have side effects
```

## Advanced Optimization Techniques

### Module Federation
```javascript
// webpack.config.js for Module Federation
const ModuleFederationPlugin = require('@module-federation/webpack');

module.exports = {
    mode: 'production',
    
    plugins: [
        new ModuleFederationPlugin({
            name: 'shell',
            remotes: {
                'user-app': 'userApp@http://localhost:3001/remoteEntry.js',
                'product-app': 'productApp@http://localhost:3002/remoteEntry.js'
            },
            shared: {
                react: {
                    singleton: true,
                    requiredVersion: '^18.0.0'
                },
                'react-dom': {
                    singleton: true,
                    requiredVersion: '^18.0.0'
                }
            }
        })
    ]
};

// Usage in shell application
import React, { Suspense, lazy } from 'react';

const UserApp = lazy(() => import('user-app/UserApp'));
const ProductApp = lazy(() => import('product-app/ProductApp'));

function App() {
    return (
        <div className="app">
            <nav>
                <a href="/users">Users</a>
                <a href="/products">Products</a>
            </nav>
            
            <main>
                <Suspense fallback={<div>Loading...</div>}>
                    <Routes>
                        <Route path="/users" element={<UserApp />} />
                        <Route path="/products" element={<ProductApp />} />
                    </Routes>
                </Suspense>
            </main>
        </div>
    );
}
```

### Dynamic Imports with Webpack
```javascript
// Dynamic import with webpack magic comments
function loadComponent(componentName) {
    return import(
        /* webpackChunkName: "[request]" */
        /* webpackMode: "lazy" */
        `./components/${componentName}`
    );
}

// Dynamic import with preloading
function preloadComponent(componentName) {
    return import(
        /* webpackChunkName: "[request]" */
        /* webpackMode: "lazy" */
        /* webpackPreload: true */
        `./components/${componentName}`
    );
}

// Dynamic import with prefetching
function prefetchComponent(componentName) {
    return import(
        /* webpackChunkName: "[request]" */
        /* webpackMode: "lazy" */
        /* webpackPrefetch: true */
        `./components/${componentName}`
    );
}

// Usage
function App() {
    const [Component, setComponent] = useState(null);
    const [loading, setLoading] = useState(false);
    
    const loadComponent = async (componentName) => {
        setLoading(true);
        try {
            const module = await loadComponent(componentName);
            setComponent(() => module.default);
        } catch (error) {
            console.error('Failed to load component:', error);
        } finally {
            setLoading(false);
        }
    };
    
    return (
        <div>
            <button onClick={() => loadComponent('Button')}>
                Load Button
            </button>
            {loading && <div>Loading...</div>}
            {Component && <Component />}
        </div>
    );
}
```

## Exercise: Bundle Optimization System

Create a comprehensive bundle optimization system with:
- Bundle analysis and monitoring
- Code splitting strategies
- Tree shaking configuration
- Dynamic imports with webpack
- Module federation setup
- Performance monitoring

## Key Takeaways
- Use bundle analyzers to identify optimization opportunities
- Implement code splitting for better performance
- Configure tree shaking to eliminate dead code
- Use dynamic imports for lazy loading
- Monitor bundle sizes in development and production
- Implement module federation for micro-frontends
- Use webpack magic comments for optimization
- Test bundle optimizations across different browsers
- Monitor performance impact of optimizations
- Document optimization strategies for team consistency
