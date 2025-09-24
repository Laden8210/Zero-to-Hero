# Advanced Hook Patterns

## Introduction
Advanced hook patterns enable sophisticated React applications with complex interactions, performance optimizations, and reusable logic. This lesson covers higher-order hooks, hook composition, and advanced patterns for production applications.

## Higher-Order Hooks

### Hook Composition Patterns
```jsx
import { useState, useEffect, useCallback, useMemo } from 'react';

// Higher-order hook for data fetching with caching
function useDataFetching(url, options = {}) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    const [cache, setCache] = useState(new Map());
    
    const fetchData = useCallback(async () => {
        // Check cache first
        if (cache.has(url)) {
            setData(cache.get(url));
            setLoading(false);
            return;
        }
        
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(url, options);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            setData(result);
            
            // Cache the result
            setCache(prev => new Map(prev).set(url, result));
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    }, [url, JSON.stringify(options), cache]);
    
    useEffect(() => {
        fetchData();
    }, [fetchData]);
    
    const refetch = useCallback(() => {
        setCache(prev => {
            const newCache = new Map(prev);
            newCache.delete(url);
            return newCache;
        });
        fetchData();
    }, [fetchData, url]);
    
    return { data, loading, error, refetch };
}

// Higher-order hook for form validation
function useFormValidation(initialValues, validationRules) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    
    const validate = useCallback(() => {
        const newErrors = {};
        
        Object.entries(validationRules).forEach(([field, rule]) => {
            const error = rule(values[field], values);
            if (error) {
                newErrors[field] = error;
            }
        });
        
        setErrors(newErrors);
        return Object.keys(newErrors).length === 0;
    }, [values, validationRules]);
    
    const setValue = useCallback((name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
        
        // Clear error when user starts typing
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: null }));
        }
    }, [errors]);
    
    const setFieldTouched = useCallback((name) => {
        setTouched(prev => ({ ...prev, [name]: true }));
    }, []);
    
    const isValid = useMemo(() => {
        return Object.keys(validationRules).every(field => {
            const rule = validationRules[field];
            return !rule(values[field], values);
        });
    }, [values, validationRules]);
    
    return {
        values,
        errors,
        touched,
        isValid,
        setValue,
        setFieldTouched,
        validate
    };
}

// Higher-order hook for pagination
function usePagination(data, itemsPerPage = 10) {
    const [currentPage, setCurrentPage] = useState(1);
    
    const totalPages = Math.ceil(data.length / itemsPerPage);
    const startIndex = (currentPage - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    const currentData = data.slice(startIndex, endIndex);
    
    const goToPage = useCallback((page) => {
        if (page >= 1 && page <= totalPages) {
            setCurrentPage(page);
        }
    }, [totalPages]);
    
    const nextPage = useCallback(() => {
        goToPage(currentPage + 1);
    }, [currentPage, goToPage]);
    
    const prevPage = useCallback(() => {
        goToPage(currentPage - 1);
    }, [currentPage, goToPage]);
    
    const resetPage = useCallback(() => {
        setCurrentPage(1);
    }, []);
    
    return {
        currentPage,
        totalPages,
        currentData,
        goToPage,
        nextPage,
        prevPage,
        resetPage,
        hasNext: currentPage < totalPages,
        hasPrev: currentPage > 1
    };
}

// Usage example
function UserManagement() {
    const { data: users, loading, error, refetch } = useDataFetching('/api/users');
    const pagination = usePagination(users || [], 5);
    
    const validationRules = {
        name: (value) => !value ? 'Name is required' : null,
        email: (value) => {
            if (!value) return 'Email is required';
            if (!/\S+@\S+\.\S+/.test(value)) return 'Email is invalid';
            return null;
        }
    };
    
    const form = useFormValidation({
        name: '',
        email: ''
    }, validationRules);
    
    const handleSubmit = (e) => {
        e.preventDefault();
        if (form.validate()) {
            console.log('Form is valid:', form.values);
            // Handle form submission
        }
    };
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <div>
            <h2>User Management</h2>
            
            <form onSubmit={handleSubmit}>
                <div>
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
                </div>
                
                <div>
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
                </div>
                
                <button type="submit" disabled={!form.isValid}>
                    Add User
                </button>
            </form>
            
            <div>
                <h3>Users ({pagination.currentData.length})</h3>
                {pagination.currentData.map(user => (
                    <div key={user.id}>
                        {user.name} - {user.email}
                    </div>
                ))}
                
                <div>
                    <button
                        onClick={pagination.prevPage}
                        disabled={!pagination.hasPrev}
                    >
                        Previous
                    </button>
                    <span>
                        Page {pagination.currentPage} of {pagination.totalPages}
                    </span>
                    <button
                        onClick={pagination.nextPage}
                        disabled={!pagination.hasNext}
                    >
                        Next
                    </button>
                </div>
            </div>
        </div>
    );
}
```

### Hook Factory Pattern
```jsx
import { useState, useEffect, useCallback } from 'react';

// Hook factory for creating resource hooks
function createResourceHook(resourceName) {
    return function useResource(id) {
        const [data, setData] = useState(null);
        const [loading, setLoading] = useState(true);
        const [error, setError] = useState(null);
        
        const fetchResource = useCallback(async () => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(`/api/${resourceName}/${id}`);
                if (!response.ok) {
                    throw new Error(`Failed to fetch ${resourceName}`);
                }
                
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        }, [id]);
        
        useEffect(() => {
            if (id) {
                fetchResource();
            }
        }, [id, fetchResource]);
        
        return { data, loading, error, refetch: fetchResource };
    };
}

// Create specific resource hooks
const useUser = createResourceHook('users');
const usePost = createResourceHook('posts');
const useComment = createResourceHook('comments');

// Hook factory for creating CRUD hooks
function createCRUDHook(resourceName) {
    return function useCRUD() {
        const [items, setItems] = useState([]);
        const [loading, setLoading] = useState(false);
        const [error, setError] = useState(null);
        
        const fetchAll = useCallback(async () => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(`/api/${resourceName}`);
                if (!response.ok) {
                    throw new Error(`Failed to fetch ${resourceName}`);
                }
                
                const result = await response.json();
                setItems(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        }, []);
        
        const create = useCallback(async (data) => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(`/api/${resourceName}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
                
                if (!response.ok) {
                    throw new Error(`Failed to create ${resourceName}`);
                }
                
                const result = await response.json();
                setItems(prev => [...prev, result]);
                return result;
            } catch (err) {
                setError(err.message);
                throw err;
            } finally {
                setLoading(false);
            }
        }, []);
        
        const update = useCallback(async (id, data) => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(`/api/${resourceName}/${id}`, {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
                
                if (!response.ok) {
                    throw new Error(`Failed to update ${resourceName}`);
                }
                
                const result = await response.json();
                setItems(prev => prev.map(item => 
                    item.id === id ? result : item
                ));
                return result;
            } catch (err) {
                setError(err.message);
                throw err;
            } finally {
                setLoading(false);
            }
        }, []);
        
        const remove = useCallback(async (id) => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(`/api/${resourceName}/${id}`, {
                    method: 'DELETE'
                });
                
                if (!response.ok) {
                    throw new Error(`Failed to delete ${resourceName}`);
                }
                
                setItems(prev => prev.filter(item => item.id !== id));
            } catch (err) {
                setError(err.message);
                throw err;
            } finally {
                setLoading(false);
            }
        }, []);
        
        return {
            items,
            loading,
            error,
            fetchAll,
            create,
            update,
            remove
        };
    };
}

// Create specific CRUD hooks
const useUsers = createCRUDHook('users');
const usePosts = createCRUDHook('posts');

// Usage
function UserProfile({ userId }) {
    const { data: user, loading, error } = useUser(userId);
    
    if (loading) return <div>Loading user...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}

function UserList() {
    const { items: users, loading, error, fetchAll, create, update, remove } = useUsers();
    
    useEffect(() => {
        fetchAll();
    }, [fetchAll]);
    
    const handleCreate = async () => {
        try {
            await create({
                name: 'New User',
                email: 'newuser@example.com'
            });
        } catch (err) {
            console.error('Failed to create user:', err);
        }
    };
    
    if (loading) return <div>Loading users...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <div>
            <h2>Users</h2>
            <button onClick={handleCreate}>Add User</button>
            {users.map(user => (
                <div key={user.id}>
                    {user.name} - {user.email}
                    <button onClick={() => remove(user.id)}>Delete</button>
                </div>
            ))}
        </div>
    );
}
```

## Advanced Hook Composition

### Hook Chaining and Composition
```jsx
import { useState, useEffect, useCallback, useMemo } from 'react';

// Base hook for API calls
function useApiCall(url, options = {}) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    const execute = useCallback(async () => {
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(url, options);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            setData(result);
            return result;
        } catch (err) {
            setError(err.message);
            throw err;
        } finally {
            setLoading(false);
        }
    }, [url, JSON.stringify(options)]);
    
    return { data, loading, error, execute };
}

// Hook for retry logic
function useRetry(apiCall, maxRetries = 3) {
    const [retryCount, setRetryCount] = useState(0);
    
    const executeWithRetry = useCallback(async () => {
        try {
            const result = await apiCall.execute();
            setRetryCount(0);
            return result;
        } catch (error) {
            if (retryCount < maxRetries) {
                setRetryCount(prev => prev + 1);
                // Wait before retry
                await new Promise(resolve => setTimeout(resolve, 1000 * retryCount));
                return executeWithRetry();
            }
            throw error;
        }
    }, [apiCall, retryCount, maxRetries]);
    
    return {
        ...apiCall,
        execute: executeWithRetry,
        retryCount
    };
}

// Hook for caching
function useCache(apiCall, cacheKey) {
    const [cache, setCache] = useState(new Map());
    
    const executeWithCache = useCallback(async () => {
        if (cache.has(cacheKey)) {
            return cache.get(cacheKey);
        }
        
        const result = await apiCall.execute();
        setCache(prev => new Map(prev).set(cacheKey, result));
        return result;
    }, [apiCall, cacheKey, cache]);
    
    const clearCache = useCallback(() => {
        setCache(prev => {
            const newCache = new Map(prev);
            newCache.delete(cacheKey);
            return newCache;
        });
    }, [cacheKey]);
    
    return {
        ...apiCall,
        execute: executeWithCache,
        clearCache
    };
}

// Hook for polling
function usePolling(apiCall, interval = 5000) {
    const [isPolling, setIsPolling] = useState(false);
    
    useEffect(() => {
        if (!isPolling) return;
        
        const timer = setInterval(() => {
            apiCall.execute();
        }, interval);
        
        return () => clearInterval(timer);
    }, [isPolling, interval, apiCall]);
    
    const startPolling = useCallback(() => {
        setIsPolling(true);
    }, []);
    
    const stopPolling = useCallback(() => {
        setIsPolling(false);
    }, []);
    
    return {
        ...apiCall,
        isPolling,
        startPolling,
        stopPolling
    };
}

// Composed hook factory
function createAdvancedApiHook(url, options = {}) {
    const baseApi = useApiCall(url, options);
    const retryApi = useRetry(baseApi);
    const cachedApi = useCache(retryApi, url);
    const pollingApi = usePolling(cachedApi);
    
    return pollingApi;
}

// Usage
function DataDisplay() {
    const api = createAdvancedApiHook('/api/data');
    
    useEffect(() => {
        api.execute();
    }, []);
    
    const handleRefresh = () => {
        api.clearCache();
        api.execute();
    };
    
    const handleStartPolling = () => {
        api.startPolling();
    };
    
    const handleStopPolling = () => {
        api.stopPolling();
    };
    
    if (api.loading) return <div>Loading...</div>;
    if (api.error) return <div>Error: {api.error}</div>;
    
    return (
        <div>
            <h2>Data Display</h2>
            <div>
                <button onClick={handleRefresh}>Refresh</button>
                <button onClick={handleStartPolling}>Start Polling</button>
                <button onClick={handleStopPolling}>Stop Polling</button>
            </div>
            <div>
                {api.isPolling && <span>Polling...</span>}
                {api.retryCount > 0 && <span>Retries: {api.retryCount}</span>}
            </div>
            <pre>{JSON.stringify(api.data, null, 2)}</pre>
        </div>
    );
}
```

## Exercise: Advanced Hook System

Create an advanced hook system with:
- Higher-order hooks for common patterns
- Hook factory patterns for resource management
- Hook composition and chaining
- Performance optimization techniques
- Error handling and retry logic
- Caching and polling capabilities

## Key Takeaways
- Use higher-order hooks to create reusable logic patterns
- Implement hook factories for generating similar hooks
- Compose hooks to build complex functionality
- Use useCallback and useMemo for performance optimization
- Implement proper error handling in custom hooks
- Create hooks that are flexible and configurable
- Test custom hooks independently from components
- Document hook APIs and usage patterns
- Use TypeScript for better type safety
- Consider the performance implications of hook composition
