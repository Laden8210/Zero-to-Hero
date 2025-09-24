# React Performance Optimization

## Introduction
React performance optimization is crucial for creating smooth, responsive applications. This lesson covers React.memo, code splitting, bundle optimization, and rendering optimization techniques.

## React.memo for Component Memoization

### Basic React.memo Usage
```jsx
import React, { useState, memo } from 'react';

// Component without memoization
function ExpensiveComponent({ data, onUpdate }) {
    console.log('ExpensiveComponent rendered');
    
    // Simulate expensive computation
    const processedData = data.map(item => ({
        ...item,
        processed: true,
        timestamp: Date.now()
    }));
    
    return (
        <div>
            <h3>Processed Data</h3>
            <ul>
                {processedData.map(item => (
                    <li key={item.id}>
                        {item.name} - {item.processed ? 'Processed' : 'Not processed'}
                    </li>
                ))}
            </ul>
            <button onClick={() => onUpdate(Math.random())}>
                Update Data
            </button>
        </div>
    );
}

// Component with memoization
const MemoizedExpensiveComponent = memo(ExpensiveComponent);

// Parent component
function ParentComponent() {
    const [data, setData] = useState([
        { id: 1, name: 'Item 1' },
        { id: 2, name: 'Item 2' },
        { id: 3, name: 'Item 3' }
    ]);
    const [counter, setCounter] = useState(0);
    
    const handleDataUpdate = (newValue) => {
        setData(prev => [...prev, { id: Date.now(), name: `Item ${newValue}` }]);
    };
    
    return (
        <div>
            <h2>Parent Component</h2>
            <p>Counter: {counter}</p>
            <button onClick={() => setCounter(counter + 1)}>
                Increment Counter
            </button>
            
            {/* This will re-render on every parent update */}
            <ExpensiveComponent data={data} onUpdate={handleDataUpdate} />
            
            {/* This will only re-render when data or onUpdate changes */}
            <MemoizedExpensiveComponent data={data} onUpdate={handleDataUpdate} />
        </div>
    );
}
```

### Custom Comparison Function
```jsx
import React, { memo } from 'react';

// Component with complex props
function UserCard({ user, settings, onEdit, onDelete }) {
    console.log('UserCard rendered for:', user.name);
    
    return (
        <div className="user-card">
            <h3>{user.name}</h3>
            <p>{user.email}</p>
            <p>Role: {user.role}</p>
            <p>Theme: {settings.theme}</p>
            <div>
                <button onClick={() => onEdit(user.id)}>Edit</button>
                <button onClick={() => onDelete(user.id)}>Delete</button>
            </div>
        </div>
    );
}

// Custom comparison function
const MemoizedUserCard = memo(UserCard, (prevProps, nextProps) => {
    // Only re-render if user data or settings actually changed
    const userChanged = prevProps.user.id !== nextProps.user.id ||
                       prevProps.user.name !== nextProps.user.name ||
                       prevProps.user.email !== nextProps.user.email ||
                       prevProps.user.role !== nextProps.user.role;
    
    const settingsChanged = prevProps.settings.theme !== nextProps.settings.theme;
    
    // Return true if props are equal (don't re-render)
    // Return false if props are different (re-render)
    return !userChanged && !settingsChanged;
});

// Usage
function UserList({ users, settings, onEdit, onDelete }) {
    return (
        <div>
            {users.map(user => (
                <MemoizedUserCard
                    key={user.id}
                    user={user}
                    settings={settings}
                    onEdit={onEdit}
                    onDelete={onDelete}
                />
            ))}
        </div>
    );
}
```

## Code Splitting and Lazy Loading

### React.lazy and Suspense
```jsx
import React, { Suspense, lazy, useState } from 'react';

// Lazy load components
const LazyDashboard = lazy(() => import('./Dashboard'));
const LazyProfile = lazy(() => import('./Profile'));
const LazySettings = lazy(() => import('./Settings'));

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
                    <h2>Something went wrong loading this component.</h2>
                    <button onClick={() => this.setState({ hasError: false })}>
                        Try again
                    </button>
                </div>
            );
        }
        
        return this.props.children;
    }
}

// Main application with lazy loading
function App() {
    const [currentPage, setCurrentPage] = useState('dashboard');
    
    const renderPage = () => {
        switch (currentPage) {
            case 'dashboard':
                return <LazyDashboard />;
            case 'profile':
                return <LazyProfile />;
            case 'settings':
                return <LazySettings />;
            default:
                return <LazyDashboard />;
        }
    };
    
    return (
        <div className="app">
            <nav>
                <button 
                    onClick={() => setCurrentPage('dashboard')}
                    className={currentPage === 'dashboard' ? 'active' : ''}
                >
                    Dashboard
                </button>
                <button 
                    onClick={() => setCurrentPage('profile')}
                    className={currentPage === 'profile' ? 'active' : ''}
                >
                    Profile
                </button>
                <button 
                    onClick={() => setCurrentPage('settings')}
                    className={currentPage === 'settings' ? 'active' : ''}
                >
                    Settings
                </button>
            </nav>
            
            <main>
                <LazyErrorBoundary>
                    <Suspense fallback={<LoadingSpinner />}>
                        {renderPage()}
                    </Suspense>
                </LazyErrorBoundary>
            </main>
        </div>
    );
}
```

### Dynamic Imports with Loading States
```jsx
import React, { useState, useEffect } from 'react';

// Hook for dynamic imports
function useDynamicImport(importFn) {
    const [Component, setComponent] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        setLoading(true);
        setError(null);
        
        importFn()
            .then(module => {
                setComponent(() => module.default);
                setLoading(false);
            })
            .catch(err => {
                setError(err);
                setLoading(false);
            });
    }, [importFn]);
    
    return { Component, loading, error };
}

// Component with dynamic import
function DynamicComponent({ componentName }) {
    const { Component, loading, error } = useDynamicImport(
        () => import(`./components/${componentName}`)
    );
    
    if (loading) {
        return <div>Loading {componentName}...</div>;
    }
    
    if (error) {
        return <div>Error loading {componentName}: {error.message}</div>;
    }
    
    if (!Component) {
        return <div>Component not found</div>;
    }
    
    return <Component />;
}

// Usage
function App() {
    const [selectedComponent, setSelectedComponent] = useState('Button');
    
    return (
        <div>
            <select 
                value={selectedComponent} 
                onChange={(e) => setSelectedComponent(e.target.value)}
            >
                <option value="Button">Button</option>
                <option value="Input">Input</option>
                <option value="Card">Card</option>
            </select>
            
            <DynamicComponent componentName={selectedComponent} />
        </div>
    );
}
```

## Bundle Analysis and Optimization

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

### Tree Shaking Optimization
```javascript
// utils/index.js - Barrel exports (can prevent tree shaking)
export { formatDate } from './date';
export { formatCurrency } from './currency';
export { validateEmail } from './validation';

// Better approach - direct imports
// import { formatDate } from './utils/date';
// import { formatCurrency } from './utils/currency';

// utils/date.js
export function formatDate(date) {
    return new Intl.DateTimeFormat('en-US').format(date);
}

export function formatTime(date) {
    return new Intl.DateTimeFormat('en-US', {
        hour: '2-digit',
        minute: '2-digit'
    }).format(date);
}

// utils/currency.js
export function formatCurrency(amount, currency = 'USD') {
    return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency
    }).format(amount);
}

// utils/validation.js
export function validateEmail(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

export function validatePhone(phone) {
    return /^\+?[\d\s-()]+$/.test(phone);
}
```

### Performance Monitoring
```jsx
import React, { useEffect, useRef } from 'react';

// Performance monitoring hook
function usePerformanceMonitor(componentName) {
    const renderStart = useRef();
    const renderCount = useRef(0);
    
    useEffect(() => {
        renderStart.current = performance.now();
        renderCount.current += 1;
        
        return () => {
            const renderTime = performance.now() - renderStart.current;
            console.log(`${componentName} render #${renderCount.current}: ${renderTime.toFixed(2)}ms`);
            
            // Log slow renders
            if (renderTime > 16) { // 60fps threshold
                console.warn(`Slow render detected in ${componentName}: ${renderTime.toFixed(2)}ms`);
            }
        };
    });
}

// Component with performance monitoring
function MonitoredComponent({ data }) {
    usePerformanceMonitor('MonitoredComponent');
    
    // Expensive operation
    const processedData = data.map(item => ({
        ...item,
        processed: true,
        timestamp: Date.now()
    }));
    
    return (
        <div>
            {processedData.map(item => (
                <div key={item.id}>{item.name}</div>
            ))}
        </div>
    );
}

// React DevTools Profiler integration
function ProfiledComponent() {
    return (
        <React.Profiler
            id="ProfiledComponent"
            onRender={(id, phase, actualDuration, baseDuration, startTime, commitTime) => {
                console.log('Profiler data:', {
                    id,
                    phase,
                    actualDuration,
                    baseDuration,
                    startTime,
                    commitTime
                });
            }}
        >
            <MonitoredComponent data={[]} />
        </React.Profiler>
    );
}
```

## Rendering Optimization Techniques

### Virtual Scrolling
```jsx
import React, { useState, useEffect, useMemo, useCallback } from 'react';

// Virtual scrolling hook
function useVirtualScrolling(items, itemHeight, containerHeight) {
    const [scrollTop, setScrollTop] = useState(0);
    
    const visibleItems = useMemo(() => {
        const startIndex = Math.floor(scrollTop / itemHeight);
        const endIndex = Math.min(
            startIndex + Math.ceil(containerHeight / itemHeight) + 1,
            items.length
        );
        
        return items.slice(startIndex, endIndex).map((item, index) => ({
            ...item,
            index: startIndex + index
        }));
    }, [items, itemHeight, containerHeight, scrollTop]);
    
    const totalHeight = items.length * itemHeight;
    const offsetY = Math.floor(scrollTop / itemHeight) * itemHeight;
    
    return {
        visibleItems,
        totalHeight,
        offsetY,
        setScrollTop
    };
}

// Virtual scrolling component
function VirtualList({ items, itemHeight = 50, containerHeight = 400 }) {
    const { visibleItems, totalHeight, offsetY, setScrollTop } = useVirtualScrolling(
        items,
        itemHeight,
        containerHeight
    );
    
    const handleScroll = useCallback((e) => {
        setScrollTop(e.target.scrollTop);
    }, [setScrollTop]);
    
    return (
        <div
            style={{
                height: containerHeight,
                overflow: 'auto'
            }}
            onScroll={handleScroll}
        >
            <div style={{ height: totalHeight, position: 'relative' }}>
                <div
                    style={{
                        transform: `translateY(${offsetY}px)`,
                        position: 'absolute',
                        top: 0,
                        left: 0,
                        right: 0
                    }}
                >
                    {visibleItems.map(item => (
                        <div
                            key={item.id}
                            style={{
                                height: itemHeight,
                                display: 'flex',
                                alignItems: 'center',
                                padding: '0 16px',
                                borderBottom: '1px solid #eee'
                            }}
                        >
                            {item.name}
                        </div>
                    ))}
                </div>
            </div>
        </div>
    );
}

// Usage
function LargeListApp() {
    const items = useMemo(() => 
        Array.from({ length: 10000 }, (_, i) => ({
            id: i,
            name: `Item ${i + 1}`
        }))
    , []);
    
    return (
        <div>
            <h2>Virtual Scrolling List (10,000 items)</h2>
            <VirtualList items={items} />
        </div>
    );
}
```

### Memoization Strategies
```jsx
import React, { memo, useMemo, useCallback } from 'react';

// Expensive calculation component
const ExpensiveCalculation = memo(({ numbers, multiplier }) => {
    const result = useMemo(() => {
        console.log('Calculating expensive result...');
        return numbers.reduce((sum, num) => sum + num * multiplier, 0);
    }, [numbers, multiplier]);
    
    return <div>Result: {result}</div>;
});

// List item component
const ListItem = memo(({ item, onSelect, onDelete }) => {
    const handleSelect = useCallback(() => {
        onSelect(item.id);
    }, [item.id, onSelect]);
    
    const handleDelete = useCallback(() => {
        onDelete(item.id);
    }, [item.id, onDelete]);
    
    return (
        <div className="list-item">
            <span>{item.name}</span>
            <button onClick={handleSelect}>Select</button>
            <button onClick={handleDelete}>Delete</button>
        </div>
    );
});

// Optimized list component
const OptimizedList = memo(({ items, onItemSelect, onItemDelete }) => {
    const handleItemSelect = useCallback((id) => {
        onItemSelect(id);
    }, [onItemSelect]);
    
    const handleItemDelete = useCallback((id) => {
        onItemDelete(id);
    }, [onItemDelete]);
    
    return (
        <div>
            {items.map(item => (
                <ListItem
                    key={item.id}
                    item={item}
                    onSelect={handleItemSelect}
                    onDelete={handleItemDelete}
                />
            ))}
        </div>
    );
});

// Main component
function PerformanceOptimizedApp() {
    const [items, setItems] = useState([]);
    const [multiplier, setMultiplier] = useState(1);
    
    const numbers = useMemo(() => 
        items.map(item => item.value), 
        [items]
    );
    
    const handleItemSelect = useCallback((id) => {
        console.log('Selected item:', id);
    }, []);
    
    const handleItemDelete = useCallback((id) => {
        setItems(prev => prev.filter(item => item.id !== id));
    }, []);
    
    return (
        <div>
            <div>
                <label>
                    Multiplier: 
                    <input
                        type="number"
                        value={multiplier}
                        onChange={(e) => setMultiplier(Number(e.target.value))}
                    />
                </label>
            </div>
            
            <ExpensiveCalculation numbers={numbers} multiplier={multiplier} />
            
            <OptimizedList
                items={items}
                onItemSelect={handleItemSelect}
                onItemDelete={handleItemDelete}
            />
        </div>
    );
}
```

## Exercise: Performance-Optimized Application

Create a performance-optimized React application with:
- React.memo for component memoization
- Code splitting with React.lazy
- Virtual scrolling for large lists
- Bundle optimization techniques
- Performance monitoring
- Memoization strategies

## Key Takeaways
- Use React.memo to prevent unnecessary re-renders
- Implement code splitting for better initial load times
- Use useMemo and useCallback for expensive operations
- Monitor bundle size and optimize imports
- Implement virtual scrolling for large datasets
- Use React DevTools Profiler for performance analysis
- Consider tree shaking when structuring code
- Optimize images and assets
- Use production builds for performance testing
- Profile and measure before optimizing
