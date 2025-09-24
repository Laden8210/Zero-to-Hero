# Hook Testing and Debugging

## Introduction
Testing and debugging custom hooks is crucial for maintaining reliable React applications. This lesson covers testing strategies, debugging techniques, and tools for custom hook development.

## Testing Custom Hooks

### Basic Hook Testing
```jsx
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
    test('should initialize with default value', () => {
        const { result } = renderHook(() => useCounter());
        
        expect(result.current.count).toBe(0);
    });
    
    test('should initialize with custom value', () => {
        const { result } = renderHook(() => useCounter(10));
        
        expect(result.current.count).toBe(10);
    });
    
    test('should increment count', () => {
        const { result } = renderHook(() => useCounter());
        
        act(() => {
            result.current.increment();
        });
        
        expect(result.current.count).toBe(1);
    });
    
    test('should decrement count', () => {
        const { result } = renderHook(() => useCounter(5));
        
        act(() => {
            result.current.decrement();
        });
        
        expect(result.current.count).toBe(4);
    });
    
    test('should reset count', () => {
        const { result } = renderHook(() => useCounter(10));
        
        act(() => {
            result.current.increment();
            result.current.reset();
        });
        
        expect(result.current.count).toBe(10);
    });
});
```

### Testing Async Hooks
```jsx
import { renderHook, waitFor } from '@testing-library/react';
import { useApi } from './useApi';

// Mock fetch
global.fetch = jest.fn();

describe('useApi', () => {
    beforeEach(() => {
        fetch.mockClear();
    });
    
    test('should fetch data successfully', async () => {
        const mockData = { id: 1, name: 'Test' };
        fetch.mockResolvedValueOnce({
            ok: true,
            json: async () => mockData
        });
        
        const { result } = renderHook(() => useApi('/api/test'));
        
        expect(result.current.loading).toBe(true);
        expect(result.current.data).toBe(null);
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.data).toEqual(mockData);
        expect(result.current.error).toBe(null);
    });
    
    test('should handle fetch error', async () => {
        fetch.mockRejectedValueOnce(new Error('Network error'));
        
        const { result } = renderHook(() => useApi('/api/test'));
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(result.current.error).toBe('Network error');
        expect(result.current.data).toBe(null);
    });
    
    test('should refetch data', async () => {
        const mockData = { id: 1, name: 'Test' };
        fetch.mockResolvedValue({
            ok: true,
            json: async () => mockData
        });
        
        const { result } = renderHook(() => useApi('/api/test'));
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        act(() => {
            result.current.refetch();
        });
        
        expect(result.current.loading).toBe(true);
        
        await waitFor(() => {
            expect(result.current.loading).toBe(false);
        });
        
        expect(fetch).toHaveBeenCalledTimes(2);
    });
});
```

### Testing Hook Dependencies
```jsx
import { renderHook, act } from '@testing-library/react';
import { useForm } from './useForm';

describe('useForm', () => {
    test('should validate form fields', () => {
        const validationRules = {
            name: (value) => !value ? 'Name is required' : null,
            email: (value) => {
                if (!value) return 'Email is required';
                if (!/\S+@\S+\.\S+/.test(value)) return 'Email is invalid';
                return null;
            }
        };
        
        const { result } = renderHook(() => 
            useForm({ name: '', email: '' }, validationRules)
        );
        
        act(() => {
            result.current.validate();
        });
        
        expect(result.current.errors.name).toBe('Name is required');
        expect(result.current.errors.email).toBe('Email is required');
        expect(result.current.isValid).toBe(false);
    });
    
    test('should clear errors when values change', () => {
        const validationRules = {
            name: (value) => !value ? 'Name is required' : null
        };
        
        const { result } = renderHook(() => 
            useForm({ name: '', email: '' }, validationRules)
        );
        
        act(() => {
            result.current.validate();
        });
        
        expect(result.current.errors.name).toBe('Name is required');
        
        act(() => {
            result.current.setValue('name', 'John');
        });
        
        expect(result.current.errors.name).toBe(null);
    });
    
    test('should handle form submission', async () => {
        const onSubmit = jest.fn();
        const validationRules = {
            name: (value) => !value ? 'Name is required' : null
        };
        
        const { result } = renderHook(() => 
            useForm({ name: 'John', email: 'john@example.com' }, validationRules)
        );
        
        await act(async () => {
            await result.current.handleSubmit(onSubmit)();
        });
        
        expect(onSubmit).toHaveBeenCalledWith({
            name: 'John',
            email: 'john@example.com'
        });
    });
});
```

## Debugging Custom Hooks

### Hook Debugging Tools
```jsx
import { useState, useEffect, useRef } from 'react';

// Debug hook for logging hook state changes
function useDebugHook(hookName, state) {
    const prevState = useRef();
    
    useEffect(() => {
        if (prevState.current !== state) {
            console.log(`[${hookName}] State changed:`, {
                previous: prevState.current,
                current: state
            });
            prevState.current = state;
        }
    });
}

// Enhanced useCounter with debugging
function useCounter(initialValue = 0, debug = false) {
    const [count, setCount] = useState(initialValue);
    
    const increment = () => {
        setCount(prev => {
            const newCount = prev + 1;
            if (debug) {
                console.log('Counter increment:', { prev, newCount });
            }
            return newCount;
        });
    };
    
    const decrement = () => {
        setCount(prev => {
            const newCount = prev - 1;
            if (debug) {
                console.log('Counter decrement:', { prev, newCount });
            }
            return newCount;
        });
    };
    
    const reset = () => {
        if (debug) {
            console.log('Counter reset:', { from: count, to: initialValue });
        }
        setCount(initialValue);
    };
    
    const state = { count, increment, decrement, reset };
    
    if (debug) {
        useDebugHook('useCounter', state);
    }
    
    return state;
}

// Debug hook for API calls
function useApiDebug(url, options = {}) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    const execute = async () => {
        console.log(`[useApi] Starting request to: ${url}`);
        console.log(`[useApi] Options:`, options);
        
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(url, options);
            console.log(`[useApi] Response status: ${response.status}`);
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            console.log(`[useApi] Response data:`, result);
            
            setData(result);
        } catch (err) {
            console.error(`[useApi] Error:`, err);
            setError(err.message);
        } finally {
            setLoading(false);
            console.log(`[useApi] Request completed`);
        }
    };
    
    useEffect(() => {
        execute();
    }, [url, JSON.stringify(options)]);
    
    return { data, loading, error, refetch: execute };
}

// Usage with debugging
function DebugExample() {
    const counter = useCounter(0, true); // Enable debugging
    const { data, loading, error } = useApiDebug('/api/test');
    
    return (
        <div>
            <h2>Debug Example</h2>
            <div>
                Count: {counter.count}
                <button onClick={counter.increment}>+</button>
                <button onClick={counter.decrement}>-</button>
                <button onClick={counter.reset}>Reset</button>
            </div>
            <div>
                {loading && <p>Loading...</p>}
                {error && <p>Error: {error}</p>}
                {data && <pre>{JSON.stringify(data, null, 2)}</pre>}
            </div>
        </div>
    );
}
```

### React DevTools Integration
```jsx
import { useState, useEffect, useRef } from 'react';

// Hook for React DevTools integration
function useDevTools(name, state) {
    const ref = useRef();
    
    useEffect(() => {
        if (process.env.NODE_ENV === 'development') {
            // Add to window for DevTools access
            if (!window.__REACT_HOOKS_DEBUG__) {
                window.__REACT_HOOKS_DEBUG__ = {};
            }
            window.__REACT_HOOKS_DEBUG__[name] = state;
        }
    });
    
    return state;
}

// Enhanced hook with DevTools support
function useFormWithDevTools(initialValues, validationRules) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    
    const setValue = (name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
        
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: null }));
        }
    };
    
    const setFieldTouched = (name) => {
        setTouched(prev => ({ ...prev, [name]: true }));
    };
    
    const validate = () => {
        const newErrors = {};
        
        Object.entries(validationRules).forEach(([field, rule]) => {
            const error = rule(values[field], values);
            if (error) {
                newErrors[field] = error;
            }
        });
        
        setErrors(newErrors);
        return Object.keys(newErrors).length === 0;
    };
    
    const state = {
        values,
        errors,
        touched,
        setValue,
        setFieldTouched,
        validate
    };
    
    return useDevTools('useForm', state);
}

// Hook for performance monitoring
function usePerformanceMonitor(hookName) {
    const renderStart = useRef();
    const renderCount = useRef(0);
    
    useEffect(() => {
        renderStart.current = performance.now();
        renderCount.current += 1;
        
        return () => {
            const renderTime = performance.now() - renderStart.current;
            console.log(`[${hookName}] Render #${renderCount.current}: ${renderTime.toFixed(2)}ms`);
            
            if (renderTime > 16) { // 60fps threshold
                console.warn(`[${hookName}] Slow render detected: ${renderTime.toFixed(2)}ms`);
            }
        };
    });
    
    return { renderCount: renderCount.current };
}

// Usage
function MonitoredComponent() {
    const { renderCount } = usePerformanceMonitor('MonitoredComponent');
    
    const form = useFormWithDevTools(
        { name: '', email: '' },
        {
            name: (value) => !value ? 'Name is required' : null,
            email: (value) => {
                if (!value) return 'Email is required';
                if (!/\S+@\S+\.\S+/.test(value)) return 'Email is invalid';
                return null;
            }
        }
    );
    
    return (
        <div>
            <h2>Monitored Component (Render #{renderCount})</h2>
            <form>
                <input
                    type="text"
                    value={form.values.name}
                    onChange={(e) => form.setValue('name', e.target.value)}
                    onBlur={() => form.setFieldTouched('name')}
                    placeholder="Name"
                />
                {form.touched.name && form.errors.name && (
                    <span>{form.errors.name}</span>
                )}
                
                <input
                    type="email"
                    value={form.values.email}
                    onChange={(e) => form.setValue('email', e.target.value)}
                    onBlur={() => form.setFieldTouched('email')}
                    placeholder="Email"
                />
                {form.touched.email && form.errors.email && (
                    <span>{form.errors.email}</span>
                )}
            </form>
        </div>
    );
}
```

## Hook Error Handling

### Error Boundary for Hooks
```jsx
import React, { Component } from 'react';

class HookErrorBoundary extends Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null };
    }
    
    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }
    
    componentDidCatch(error, errorInfo) {
        console.error('Hook Error Boundary caught an error:', error, errorInfo);
        
        // Log to error reporting service
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
                    <h2>Something went wrong with a hook</h2>
                    <details>
                        <summary>Error Details</summary>
                        <pre>{this.state.error && this.state.error.toString()}</pre>
                    </details>
                </div>
            );
        }
        
        return this.props.children;
    }
}

// Safe hook wrapper
function useSafeHook(hook, fallback = null) {
    try {
        return hook();
    } catch (error) {
        console.error('Hook error:', error);
        return fallback;
    }
}

// Usage
function App() {
    return (
        <HookErrorBoundary>
            <div className="app">
                <MonitoredComponent />
            </div>
        </HookErrorBoundary>
    );
}
```

## Exercise: Hook Testing and Debugging

Create a comprehensive testing and debugging system for custom hooks:
- Unit tests for custom hooks
- Integration tests for hook interactions
- Debugging tools and logging
- Performance monitoring
- Error handling and boundaries
- React DevTools integration

## Key Takeaways
- Use @testing-library/react-hooks for testing custom hooks
- Test both synchronous and asynchronous hook behavior
- Mock external dependencies in hook tests
- Use debugging tools to monitor hook state changes
- Implement error boundaries for hook errors
- Monitor hook performance with render timing
- Use React DevTools for hook inspection
- Test hook edge cases and error conditions
- Document hook testing strategies
- Use TypeScript for better hook type safety
