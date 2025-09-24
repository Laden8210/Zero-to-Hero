# Error Handling and User Experience

## Introduction
Robust error handling and excellent user experience are crucial for production applications. This lesson covers error boundaries, loading states, retry mechanisms, and user feedback patterns.

## Comprehensive Error Boundaries

### React Error Boundaries
```jsx
import React from 'react';

// Error boundary class component
class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null, errorInfo: null };
    }
    
    static getDerivedStateFromError(error) {
        // Update state so the next render will show the fallback UI
        return { hasError: true };
    }
    
    componentDidCatch(error, errorInfo) {
        // Log error details
        console.error('Error caught by boundary:', error, errorInfo);
        
        this.setState({
            error: error,
            errorInfo: errorInfo
        });
        
        // Report error to monitoring service
        this.reportError(error, errorInfo);
    }
    
    reportError = (error, errorInfo) => {
        // Send error to monitoring service (e.g., Sentry, LogRocket)
        if (window.Sentry) {
            window.Sentry.captureException(error, {
                contexts: {
                    react: {
                        componentStack: errorInfo.componentStack
                    }
                }
            });
        }
        
        // Log to console in development
        if (process.env.NODE_ENV === 'development') {
            console.group('Error Boundary');
            console.error('Error:', error);
            console.error('Error Info:', errorInfo);
            console.groupEnd();
        }
    };
    
    handleRetry = () => {
        this.setState({ hasError: false, error: null, errorInfo: null });
    };
    
    render() {
        if (this.state.hasError) {
            // Custom fallback UI
            if (this.props.fallback) {
                return this.props.fallback(this.state.error, this.handleRetry);
            }
            
            // Default fallback UI
            return (
                <div className="error-boundary">
                    <div className="error-content">
                        <h2>Something went wrong</h2>
                        <p>We're sorry, but something unexpected happened.</p>
                        
                        {process.env.NODE_ENV === 'development' && (
                            <details className="error-details">
                                <summary>Error Details</summary>
                                <pre>{this.state.error && this.state.error.toString()}</pre>
                                <pre>{this.state.errorInfo.componentStack}</pre>
                            </details>
                        )}
                        
                        <div className="error-actions">
                            <button onClick={this.handleRetry} className="btn btn-primary">
                                Try Again
                            </button>
                            <button 
                                onClick={() => window.location.reload()} 
                                className="btn btn-secondary"
                            >
                                Reload Page
                            </button>
                        </div>
                    </div>
                </div>
            );
        }
        
        return this.props.children;
    }
}

// Custom error boundary with different fallbacks
function ApiErrorBoundary({ children }) {
    return (
        <ErrorBoundary
            fallback={(error, retry) => (
                <div className="api-error-fallback">
                    <div className="error-icon">⚠️</div>
                    <h3>API Error</h3>
                    <p>Failed to load data from the server.</p>
                    <button onClick={retry} className="btn btn-primary">
                        Retry Request
                    </button>
                </div>
            )}
        >
            {children}
        </ErrorBoundary>
    );
}

// Usage
function App() {
    return (
        <ErrorBoundary>
            <div className="app">
                <ApiErrorBoundary>
                    <UserList />
                </ApiErrorBoundary>
                
                <ErrorBoundary>
                    <Dashboard />
                </ErrorBoundary>
            </div>
        </ErrorBoundary>
    );
}
```

### Error Boundary Hooks (React 18+)
```jsx
import { useErrorHandler } from 'react-error-boundary';

// Custom hook for error handling
function useAsyncError() {
    const handleError = useErrorHandler();
    
    return (error) => {
        handleError(error);
    };
}

// Async component with error handling
function AsyncComponent() {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const throwError = useAsyncError();
    
    useEffect(() => {
        async function fetchData() {
            try {
                const response = await fetch('/api/data');
                if (!response.ok) {
                    throw new Error(`HTTP ${response.status}`);
                }
                const result = await response.json();
                setData(result);
            } catch (error) {
                throwError(error);
            } finally {
                setLoading(false);
            }
        }
        
        fetchData();
    }, [throwError]);
    
    if (loading) return <div>Loading...</div>;
    
    return <div>{JSON.stringify(data)}</div>;
}

// Error boundary with hooks
function ErrorFallback({ error, resetErrorBoundary }) {
    return (
        <div className="error-fallback">
            <h2>Something went wrong:</h2>
            <pre>{error.message}</pre>
            <button onClick={resetErrorBoundary}>Try again</button>
        </div>
    );
}

// Usage with react-error-boundary
import { ErrorBoundary } from 'react-error-boundary';

function App() {
    return (
        <ErrorBoundary
            FallbackComponent={ErrorFallback}
            onError={(error, errorInfo) => {
                console.error('Error caught:', error, errorInfo);
            }}
            onReset={() => {
                // Reset app state
                window.location.reload();
            }}
        >
            <AsyncComponent />
        </ErrorBoundary>
    );
}
```

## Loading State Management

### Loading States and Skeletons
```jsx
// Loading skeleton components
function SkeletonLoader({ lines = 3, className = '' }) {
    return (
        <div className={`animate-pulse ${className}`}>
            {Array.from({ length: lines }).map((_, index) => (
                <div
                    key={index}
                    className="h-4 bg-gray-200 rounded mb-2"
                    style={{
                        width: `${Math.random() * 40 + 60}%`
                    }}
                />
            ))}
        </div>
    );
}

function CardSkeleton() {
    return (
        <div className="bg-white rounded-lg shadow-md p-6">
            <div className="animate-pulse">
                <div className="h-4 bg-gray-200 rounded w-3/4 mb-4"></div>
                <div className="h-3 bg-gray-200 rounded w-1/2 mb-2"></div>
                <div className="h-3 bg-gray-200 rounded w-2/3"></div>
            </div>
        </div>
    );
}

function TableSkeleton({ rows = 5 }) {
    return (
        <div className="animate-pulse">
            <div className="h-4 bg-gray-200 rounded mb-4"></div>
            {Array.from({ length: rows }).map((_, index) => (
                <div key={index} className="flex space-x-4 mb-2">
                    <div className="h-3 bg-gray-200 rounded w-1/4"></div>
                    <div className="h-3 bg-gray-200 rounded w-1/3"></div>
                    <div className="h-3 bg-gray-200 rounded w-1/5"></div>
                </div>
            ))}
        </div>
    );
}

// Loading state hook
function useLoadingState(initialState = false) {
    const [loading, setLoading] = useState(initialState);
    const [error, setError] = useState(null);
    
    const execute = useCallback(async (asyncFunction) => {
        setLoading(true);
        setError(null);
        
        try {
            const result = await asyncFunction();
            return result;
        } catch (err) {
            setError(err);
            throw err;
        } finally {
            setLoading(false);
        }
    }, []);
    
    return { loading, error, execute };
}

// Component with loading states
function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const { loading, error, execute } = useLoadingState();
    
    useEffect(() => {
        execute(async () => {
            const response = await fetch(`/api/users/${userId}`);
            const userData = await response.json();
            setUser(userData);
        });
    }, [userId, execute]);
    
    if (loading) {
        return (
            <div className="user-profile">
                <div className="animate-pulse">
                    <div className="h-8 bg-gray-200 rounded w-1/3 mb-4"></div>
                    <div className="h-4 bg-gray-200 rounded w-1/2 mb-2"></div>
                    <div className="h-4 bg-gray-200 rounded w-2/3"></div>
                </div>
            </div>
        );
    }
    
    if (error) {
        return (
            <div className="error-state">
                <p>Failed to load user profile</p>
                <button onClick={() => window.location.reload()}>
                    Retry
                </button>
            </div>
        );
    }
    
    return (
        <div className="user-profile">
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}
```

### Progressive Content Loading
```jsx
// Progressive loading component
function ProgressiveLoader({ children, fallback, delay = 200 }) {
    const [showContent, setShowContent] = useState(false);
    
    useEffect(() => {
        const timer = setTimeout(() => {
            setShowContent(true);
        }, delay);
        
        return () => clearTimeout(timer);
    }, [delay]);
    
    if (!showContent) {
        return fallback || <div>Loading...</div>;
    }
    
    return children;
}

// Intersection observer for lazy loading
function LazyLoadComponent({ children, fallback, threshold = 0.1 }) {
    const [isVisible, setIsVisible] = useState(false);
    const ref = useRef();
    
    useEffect(() => {
        const observer = new IntersectionObserver(
            ([entry]) => {
                if (entry.isIntersecting) {
                    setIsVisible(true);
                    observer.disconnect();
                }
            },
            { threshold }
        );
        
        if (ref.current) {
            observer.observe(ref.current);
        }
        
        return () => observer.disconnect();
    }, [threshold]);
    
    return (
        <div ref={ref}>
            {isVisible ? children : fallback}
        </div>
    );
}

// Usage
function App() {
    return (
        <div>
            <ProgressiveLoader
                fallback={<SkeletonLoader lines={3} />}
                delay={300}
            >
                <UserProfile userId={1} />
            </ProgressiveLoader>
            
            <LazyLoadComponent
                fallback={<CardSkeleton />}
            >
                <ExpensiveComponent />
            </LazyLoadComponent>
        </div>
    );
}
```

## Retry Mechanisms

### Exponential Backoff Retry
```javascript
// Retry utility with exponential backoff
class RetryManager {
    static async retry(
        asyncFunction,
        options = {
            maxAttempts: 3,
            baseDelay: 1000,
            maxDelay: 10000,
            backoffFactor: 2,
            jitter: true
        }
    ) {
        const { maxAttempts, baseDelay, maxDelay, backoffFactor, jitter } = options;
        let lastError;
        
        for (let attempt = 1; attempt <= maxAttempts; attempt++) {
            try {
                return await asyncFunction();
            } catch (error) {
                lastError = error;
                
                if (attempt === maxAttempts) {
                    throw error;
                }
                
                // Calculate delay with exponential backoff
                const delay = Math.min(
                    baseDelay * Math.pow(backoffFactor, attempt - 1),
                    maxDelay
                );
                
                // Add jitter to prevent thundering herd
                const jitteredDelay = jitter 
                    ? delay + Math.random() * delay * 0.1
                    : delay;
                
                console.log(`Attempt ${attempt} failed, retrying in ${jitteredDelay}ms`);
                
                await this.delay(jitteredDelay);
            }
        }
        
        throw lastError;
    }
    
    static delay(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }
}

// Usage
async function fetchUserData(userId) {
    return RetryManager.retry(
        async () => {
            const response = await fetch(`/api/users/${userId}`);
            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }
            return response.json();
        },
        {
            maxAttempts: 3,
            baseDelay: 1000,
            maxDelay: 5000,
            backoffFactor: 2,
            jitter: true
        }
    );
}
```

### Circuit Breaker Pattern
```javascript
// Circuit breaker implementation
class CircuitBreaker {
    constructor(options = {}) {
        this.failureThreshold = options.failureThreshold || 5;
        this.timeout = options.timeout || 60000; // 1 minute
        this.resetTimeout = options.resetTimeout || 30000; // 30 seconds
        
        this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
        this.failureCount = 0;
        this.lastFailureTime = null;
        this.nextAttempt = null;
    }
    
    async execute(asyncFunction) {
        if (this.state === 'OPEN') {
            if (Date.now() < this.nextAttempt) {
                throw new Error('Circuit breaker is OPEN');
            }
            this.state = 'HALF_OPEN';
        }
        
        try {
            const result = await Promise.race([
                asyncFunction(),
                this.timeoutPromise()
            ]);
            
            this.onSuccess();
            return result;
        } catch (error) {
            this.onFailure();
            throw error;
        }
    }
    
    onSuccess() {
        this.failureCount = 0;
        this.state = 'CLOSED';
    }
    
    onFailure() {
        this.failureCount++;
        this.lastFailureTime = Date.now();
        
        if (this.failureCount >= this.failureThreshold) {
            this.state = 'OPEN';
            this.nextAttempt = Date.now() + this.resetTimeout;
        }
    }
    
    timeoutPromise() {
        return new Promise((_, reject) => {
            setTimeout(() => reject(new Error('Request timeout')), this.timeout);
        });
    }
    
    getState() {
        return {
            state: this.state,
            failureCount: this.failureCount,
            lastFailureTime: this.lastFailureTime,
            nextAttempt: this.nextAttempt
        };
    }
}

// Usage
const circuitBreaker = new CircuitBreaker({
    failureThreshold: 3,
    timeout: 5000,
    resetTimeout: 30000
});

async function apiCall() {
    return circuitBreaker.execute(async () => {
        const response = await fetch('/api/data');
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
        }
        return response.json();
    });
}
```

## User Feedback Patterns

### Toast Notifications
```jsx
// Toast notification system
class ToastManager {
    constructor() {
        this.toasts = [];
        this.listeners = [];
    }
    
    show(message, type = 'info', duration = 5000) {
        const toast = {
            id: Date.now(),
            message,
            type,
            duration
        };
        
        this.toasts.push(toast);
        this.notifyListeners();
        
        // Auto remove
        setTimeout(() => {
            this.remove(toast.id);
        }, duration);
        
        return toast.id;
    }
    
    remove(id) {
        this.toasts = this.toasts.filter(toast => toast.id !== id);
        this.notifyListeners();
    }
    
    subscribe(listener) {
        this.listeners.push(listener);
        return () => {
            this.listeners = this.listeners.filter(l => l !== listener);
        };
    }
    
    notifyListeners() {
        this.listeners.forEach(listener => listener(this.toasts));
    }
}

const toastManager = new ToastManager();

// Toast component
function Toast({ toast, onRemove }) {
    const [isVisible, setIsVisible] = useState(false);
    
    useEffect(() => {
        setIsVisible(true);
    }, []);
    
    const handleRemove = () => {
        setIsVisible(false);
        setTimeout(() => onRemove(toast.id), 300);
    };
    
    const typeClasses = {
        success: 'bg-green-500 text-white',
        error: 'bg-red-500 text-white',
        warning: 'bg-yellow-500 text-black',
        info: 'bg-blue-500 text-white'
    };
    
    return (
        <div
            className={`toast ${typeClasses[toast.type]} ${
                isVisible ? 'toast-visible' : 'toast-hidden'
            }`}
        >
            <span>{toast.message}</span>
            <button onClick={handleRemove} className="toast-close">
                ×
            </button>
        </div>
    );
}

// Toast container
function ToastContainer() {
    const [toasts, setToasts] = useState([]);
    
    useEffect(() => {
        const unsubscribe = toastManager.subscribe(setToasts);
        return unsubscribe;
    }, []);
    
    return (
        <div className="toast-container">
            {toasts.map(toast => (
                <Toast
                    key={toast.id}
                    toast={toast}
                    onRemove={(id) => toastManager.remove(id)}
                />
            ))}
        </div>
    );
}

// Usage
function App() {
    const handleSuccess = () => {
        toastManager.show('Operation completed successfully!', 'success');
    };
    
    const handleError = () => {
        toastManager.show('Something went wrong!', 'error');
    };
    
    return (
        <div>
            <ToastContainer />
            <button onClick={handleSuccess}>Success</button>
            <button onClick={handleError}>Error</button>
        </div>
    );
}
```

## Exercise: Error Handling Application

Create an application with comprehensive error handling:
- React error boundaries with custom fallbacks
- Loading states with skeleton screens
- Retry mechanisms with exponential backoff
- Circuit breaker pattern for API calls
- Toast notifications for user feedback
- Progressive loading and lazy loading

## Key Takeaways
- Implement error boundaries to catch and handle React errors
- Use loading states and skeleton screens for better UX
- Implement retry mechanisms with exponential backoff
- Use circuit breaker pattern for resilient API calls
- Provide clear error messages and recovery options
- Use toast notifications for user feedback
- Implement progressive loading for better perceived performance
- Monitor and log errors for debugging
- Test error scenarios thoroughly
- Design error states as part of the user experience
