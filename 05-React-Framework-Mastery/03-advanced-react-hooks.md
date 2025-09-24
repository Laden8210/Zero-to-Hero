# Advanced React Hooks

## Introduction
Advanced React hooks provide powerful tools for performance optimization, logic reuse, and complex state management. This lesson covers useCallback, useMemo, custom hooks, and advanced patterns.

## useCallback for Function Memoization

### Basic useCallback Usage
```jsx
import React, { useState, useCallback } from 'react';

function ParentComponent() {
    const [count, setCount] = useState(0);
    const [name, setName] = useState('');
    
    // Without useCallback - function recreated on every render
    const handleClick = () => {
        console.log('Button clicked');
    };
    
    // With useCallback - function memoized
    const handleClickMemoized = useCallback(() => {
        console.log('Button clicked');
    }, []); // Empty dependency array - function never changes
    
    // useCallback with dependencies
    const handleNameChange = useCallback((event) => {
        setName(event.target.value);
    }, []); // No dependencies needed since setName is stable
    
    const handleCountIncrement = useCallback(() => {
        setCount(prevCount => prevCount + 1);
    }, []); // No dependencies needed since setCount is stable
    
    return (
        <div>
            <input 
                value={name}
                onChange={handleNameChange}
                placeholder="Enter name"
            />
            <p>Count: {count}</p>
            <button onClick={handleCountIncrement}>Increment</button>
            
            <ChildComponent 
                onButtonClick={handleClickMemoized}
                name={name}
            />
        </div>
    );
}

// Child component that receives memoized function
const ChildComponent = React.memo(({ onButtonClick, name }) => {
    console.log('ChildComponent rendered');
    
    return (
        <div>
            <p>Hello, {name}!</p>
            <button onClick={onButtonClick}>Click me</button>
        </div>
    );
});
```

### useCallback with Dependencies
```jsx
import React, { useState, useCallback, useEffect } from 'react';

function SearchComponent() {
    const [query, setQuery] = useState('');
    const [results, setResults] = useState([]);
    const [isLoading, setIsLoading] = useState(false);
    
    // useCallback with dependencies
    const performSearch = useCallback(async (searchQuery) => {
        if (!searchQuery.trim()) {
            setResults([]);
            return;
        }
        
        setIsLoading(true);
        try {
            const response = await fetch(`/api/search?q=${encodeURIComponent(searchQuery)}`);
            const data = await response.json();
            setResults(data.results);
        } catch (error) {
            console.error('Search failed:', error);
            setResults([]);
        } finally {
            setIsLoading(false);
        }
    }, []); // No dependencies - function is stable
    
    // Debounced search function
    const debouncedSearch = useCallback(
        debounce(performSearch, 300),
        [performSearch]
    );
    
    // Effect that runs when query changes
    useEffect(() => {
        debouncedSearch(query);
    }, [query, debouncedSearch]);
    
    return (
        <div>
            <input
                type="text"
                value={query}
                onChange={(e) => setQuery(e.target.value)}
                placeholder="Search..."
            />
            
            {isLoading && <p>Loading...</p>}
            
            <ul>
                {results.map((result) => (
                    <li key={result.id}>{result.title}</li>
                ))}
            </ul>
        </div>
    );
}

// Debounce utility function
function debounce(func, wait) {
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
```

## useMemo for Expensive Computation Caching

### Basic useMemo Usage
```jsx
import React, { useState, useMemo } from 'react';

function ExpensiveCalculationComponent() {
    const [count, setCount] = useState(0);
    const [multiplier, setMultiplier] = useState(1);
    
    // Expensive calculation without useMemo
    const expensiveValue = calculateExpensiveValue(count, multiplier);
    
    // Expensive calculation with useMemo
    const memoizedValue = useMemo(() => {
        console.log('Calculating expensive value...');
        return calculateExpensiveValue(count, multiplier);
    }, [count, multiplier]); // Only recalculate when count or multiplier changes
    
    // Complex data processing
    const processedData = useMemo(() => {
        console.log('Processing data...');
        return Array.from({ length: 1000 }, (_, i) => ({
            id: i,
            value: i * count * multiplier,
            processed: true
        }));
    }, [count, multiplier]);
    
    return (
        <div>
            <div>
                <label>
                    Count: 
                    <input
                        type="number"
                        value={count}
                        onChange={(e) => setCount(Number(e.target.value))}
                    />
                </label>
            </div>
            
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
            
            <p>Expensive Value: {memoizedValue}</p>
            <p>Processed Items: {processedData.length}</p>
            
            <button onClick={() => setCount(count + 1)}>
                Increment Count
            </button>
        </div>
    );
}

// Simulate expensive calculation
function calculateExpensiveValue(count, multiplier) {
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
        result += Math.sqrt(i * count * multiplier);
    }
    return Math.round(result);
}
```

### useMemo for Object and Array References
```jsx
import React, { useState, useMemo } from 'react';

function UserListComponent() {
    const [users, setUsers] = useState([]);
    const [filter, setFilter] = useState('');
    const [sortBy, setSortBy] = useState('name');
    
    // Memoized filtered and sorted users
    const filteredAndSortedUsers = useMemo(() => {
        console.log('Filtering and sorting users...');
        
        let filtered = users.filter(user => 
            user.name.toLowerCase().includes(filter.toLowerCase()) ||
            user.email.toLowerCase().includes(filter.toLowerCase())
        );
        
        return filtered.sort((a, b) => {
            if (sortBy === 'name') {
                return a.name.localeCompare(b.name);
            } else if (sortBy === 'email') {
                return a.email.localeCompare(b.email);
            } else if (sortBy === 'age') {
                return a.age - b.age;
            }
            return 0;
        });
    }, [users, filter, sortBy]);
    
    // Memoized configuration object
    const config = useMemo(() => ({
        itemsPerPage: 10,
        showEmail: true,
        showAge: true,
        theme: 'light'
    }), []); // Empty dependency array - config never changes
    
    // Memoized event handlers
    const handleUserSelect = useMemo(() => {
        return (userId) => {
            console.log('Selected user:', userId);
            // Handle user selection
        };
    }, []);
    
    return (
        <div>
            <div>
                <input
                    type="text"
                    value={filter}
                    onChange={(e) => setFilter(e.target.value)}
                    placeholder="Filter users..."
                />
                
                <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
                    <option value="name">Sort by Name</option>
                    <option value="email">Sort by Email</option>
                    <option value="age">Sort by Age</option>
                </select>
            </div>
            
            <UserList 
                users={filteredAndSortedUsers}
                config={config}
                onUserSelect={handleUserSelect}
            />
        </div>
    );
}

const UserList = React.memo(({ users, config, onUserSelect }) => {
    console.log('UserList rendered');
    
    return (
        <ul>
            {users.map(user => (
                <li key={user.id} onClick={() => onUserSelect(user.id)}>
                    <span>{user.name}</span>
                    {config.showEmail && <span> - {user.email}</span>}
                    {config.showAge && <span> - {user.age}</span>}
                </li>
            ))}
        </ul>
    );
});
```

## Custom Hooks Development

### Basic Custom Hook
```jsx
import { useState, useEffect } from 'react';

// Custom hook for API data fetching
function useApi(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        let isCancelled = false;
        
        const fetchData = async () => {
            try {
                setLoading(true);
                setError(null);
                
                const response = await fetch(url);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                
                const result = await response.json();
                
                if (!isCancelled) {
                    setData(result);
                }
            } catch (err) {
                if (!isCancelled) {
                    setError(err.message);
                }
            } finally {
                if (!isCancelled) {
                    setLoading(false);
                }
            }
        };
        
        fetchData();
        
        return () => {
            isCancelled = true;
        };
    }, [url]);
    
    return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
    const { data: user, loading, error } = useApi(`/api/users/${userId}`);
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}
```

### Advanced Custom Hooks
```jsx
import { useState, useEffect, useCallback, useRef } from 'react';

// Custom hook for local storage
function useLocalStorage(key, initialValue) {
    const [storedValue, setStoredValue] = useState(() => {
        try {
            const item = window.localStorage.getItem(key);
            return item ? JSON.parse(item) : initialValue;
        } catch (error) {
            console.error(`Error reading localStorage key "${key}":`, error);
            return initialValue;
        }
    });
    
    const setValue = useCallback((value) => {
        try {
            const valueToStore = value instanceof Function ? value(storedValue) : value;
            setStoredValue(valueToStore);
            window.localStorage.setItem(key, JSON.stringify(valueToStore));
        } catch (error) {
            console.error(`Error setting localStorage key "${key}":`, error);
        }
    }, [key, storedValue]);
    
    return [storedValue, setValue];
}

// Custom hook for debounced value
function useDebounce(value, delay) {
    const [debouncedValue, setDebouncedValue] = useState(value);
    
    useEffect(() => {
        const handler = setTimeout(() => {
            setDebouncedValue(value);
        }, delay);
        
        return () => {
            clearTimeout(handler);
        };
    }, [value, delay]);
    
    return debouncedValue;
}

// Custom hook for previous value
function usePrevious(value) {
    const ref = useRef();
    
    useEffect(() => {
        ref.current = value;
    });
    
    return ref.current;
}

// Custom hook for intersection observer
function useIntersectionObserver(options = {}) {
    const [isIntersecting, setIsIntersecting] = useState(false);
    const [hasIntersected, setHasIntersected] = useState(false);
    const ref = useRef();
    
    useEffect(() => {
        const element = ref.current;
        if (!element) return;
        
        const observer = new IntersectionObserver(([entry]) => {
            setIsIntersecting(entry.isIntersecting);
            if (entry.isIntersecting && !hasIntersected) {
                setHasIntersected(true);
            }
        }, options);
        
        observer.observe(element);
        
        return () => {
            observer.unobserve(element);
        };
    }, [options, hasIntersected]);
    
    return [ref, isIntersecting, hasIntersected];
}

// Custom hook for form handling
function useForm(initialValues, validationRules = {}) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    
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
    
    const handleChange = useCallback((event) => {
        const { name, value, type, checked } = event.target;
        setValue(name, type === 'checkbox' ? checked : value);
    }, [setValue]);
    
    const handleBlur = useCallback((event) => {
        const { name } = event.target;
        setFieldTouched(name);
    }, [setFieldTouched]);
    
    const handleSubmit = useCallback((onSubmit) => {
        return (event) => {
            event.preventDefault();
            if (validate()) {
                onSubmit(values);
            }
        };
    }, [values, validate]);
    
    return {
        values,
        errors,
        touched,
        setValue,
        setFieldTouched,
        validate,
        handleChange,
        handleBlur,
        handleSubmit
    };
}

// Usage examples
function SearchComponent() {
    const [query, setQuery] = useState('');
    const debouncedQuery = useDebounce(query, 300);
    const previousQuery = usePrevious(debouncedQuery);
    
    useEffect(() => {
        if (debouncedQuery && debouncedQuery !== previousQuery) {
            // Perform search
            console.log('Searching for:', debouncedQuery);
        }
    }, [debouncedQuery, previousQuery]);
    
    return (
        <input
            type="text"
            value={query}
            onChange={(e) => setQuery(e.target.value)}
            placeholder="Search..."
        />
    );
}

function LazyComponent() {
    const [ref, isIntersecting, hasIntersected] = useIntersectionObserver({
        threshold: 0.1
    });
    
    return (
        <div ref={ref}>
            {isIntersecting && <p>I'm visible!</p>}
            {hasIntersected && <p>I've been visible at least once!</p>}
        </div>
    );
}

function FormComponent() {
    const { values, errors, handleChange, handleBlur, handleSubmit } = useForm(
        { name: '', email: '' },
        {
            name: (value) => !value ? 'Name is required' : null,
            email: (value) => !value || !value.includes('@') ? 'Valid email is required' : null
        }
    );
    
    const onSubmit = (formValues) => {
        console.log('Form submitted:', formValues);
    };
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input
                name="name"
                value={values.name}
                onChange={handleChange}
                onBlur={handleBlur}
                placeholder="Name"
            />
            {errors.name && <span>{errors.name}</span>}
            
            <input
                name="email"
                type="email"
                value={values.email}
                onChange={handleChange}
                onBlur={handleBlur}
                placeholder="Email"
            />
            {errors.email && <span>{errors.email}</span>}
            
            <button type="submit">Submit</button>
        </form>
    );
}
```

## Exercise: Advanced Hooks Application

Create an application using advanced React hooks:
- useCallback for memoized event handlers
- useMemo for expensive calculations
- Custom hooks for reusable logic
- Performance optimization with React.memo
- Complex state management with custom hooks

## Key Takeaways
- Use useCallback to memoize functions and prevent unnecessary re-renders
- Use useMemo to cache expensive calculations and object references
- Create custom hooks for reusable logic across components
- Custom hooks should follow the "use" naming convention
- Use dependency arrays correctly to control when hooks update
- Combine multiple hooks to create powerful abstractions
- Test custom hooks independently
- Document custom hooks with JSDoc comments
- Consider performance implications of hook dependencies
- Use React.memo with memoized props for optimal performance
