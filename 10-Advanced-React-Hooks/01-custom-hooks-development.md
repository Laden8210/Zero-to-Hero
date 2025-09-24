# Custom Hooks Development

## Introduction
Custom hooks are a powerful way to extract and reuse stateful logic in React applications. This lesson covers creating custom hooks, sharing logic between components, and building reusable hook libraries.

## Basic Custom Hooks

### Simple Custom Hooks
```jsx
import { useState, useEffect } from 'react';

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
    
    const setValue = (value) => {
        try {
            const valueToStore = value instanceof Function ? value(storedValue) : value;
            setStoredValue(valueToStore);
            window.localStorage.setItem(key, JSON.stringify(valueToStore));
        } catch (error) {
            console.error(`Error setting localStorage key "${key}":`, error);
        }
    };
    
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

// Custom hook for window size
function useWindowSize() {
    const [windowSize, setWindowSize] = useState({
        width: undefined,
        height: undefined,
    });
    
    useEffect(() => {
        function handleResize() {
            setWindowSize({
                width: window.innerWidth,
                height: window.innerHeight,
            });
        }
        
        window.addEventListener('resize', handleResize);
        handleResize(); // Call handler right away so state gets updated with initial window size
        
        return () => window.removeEventListener('resize', handleResize);
    }, []);
    
    return windowSize;
}

// Usage examples
function App() {
    const [name, setName] = useLocalStorage('name', '');
    const [searchTerm, setSearchTerm] = useState('');
    const debouncedSearchTerm = useDebounce(searchTerm, 500);
    const previousSearchTerm = usePrevious(debouncedSearchTerm);
    const { width, height } = useWindowSize();
    
    useEffect(() => {
        if (debouncedSearchTerm !== previousSearchTerm) {
            console.log('Searching for:', debouncedSearchTerm);
        }
    }, [debouncedSearchTerm, previousSearchTerm]);
    
    return (
        <div>
            <h1>Window size: {width} x {height}</h1>
            <input
                type="text"
                value={name}
                onChange={(e) => setName(e.target.value)}
                placeholder="Enter your name"
            />
            <input
                type="text"
                value={searchTerm}
                onChange={(e) => setSearchTerm(e.target.value)}
                placeholder="Search..."
            />
        </div>
    );
}
```

**Code Explanation:**
- `function useLocalStorage(key, initialValue)`: Custom hook that syncs state with localStorage
- `useState(() => {...})`: Lazy initial state that reads from localStorage on first render
- `window.localStorage.getItem(key)`: Retrieves stored value from localStorage
- `JSON.parse(item)`: Converts stored JSON string back to JavaScript object
- `value instanceof Function ? value(storedValue) : value`: Handles both direct values and function updates
- `useDebounce(value, delay)`: Custom hook that delays value updates by specified delay
- `setTimeout(() => {...}, delay)`: Creates timer to delay value update
- `clearTimeout(handler)`: Cleans up timer on unmount or dependency change
- `usePrevious(value)`: Custom hook that tracks previous value using useRef
- `useRef()`: Creates mutable ref that persists across renders
- `useWindowSize()`: Custom hook that tracks window dimensions
- `window.addEventListener('resize', handleResize)`: Listens for window resize events
- `window.removeEventListener('resize', handleResize)`: Cleans up event listener
- `const [name, setName] = useLocalStorage('name', '')`: Uses custom hook like built-in hooks

**Benefits:**
- Encapsulates complex logic in reusable functions
- Provides consistent API across components
- Handles edge cases and error scenarios
- Improves code organization and maintainability
- Enables easy testing of isolated logic

### API Data Fetching Hooks
```jsx
import { useState, useEffect, useCallback } from 'react';

// Custom hook for API data fetching
function useApi(url, options = {}) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    const fetchData = useCallback(async () => {
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(url, options);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            setData(result);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    }, [url, JSON.stringify(options)]);
    
    useEffect(() => {
        fetchData();
    }, [fetchData]);
    
    return { data, loading, error, refetch: fetchData };
}

// Custom hook for paginated data
function usePaginatedApi(baseUrl, pageSize = 10) {
    const [page, setPage] = useState(1);
    const [data, setData] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    const [hasMore, setHasMore] = useState(true);
    
    const fetchPage = useCallback(async (pageNum) => {
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(`${baseUrl}?page=${pageNum}&limit=${pageSize}`);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            
            if (pageNum === 1) {
                setData(result.data);
            } else {
                setData(prev => [...prev, ...result.data]);
            }
            
            setHasMore(result.hasMore);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    }, [baseUrl, pageSize]);
    
    useEffect(() => {
        fetchPage(page);
    }, [page, fetchPage]);
    
    const loadMore = () => {
        if (!loading && hasMore) {
            setPage(prev => prev + 1);
        }
    };
    
    const reset = () => {
        setPage(1);
        setData([]);
        setHasMore(true);
    };
    
    return { data, loading, error, hasMore, loadMore, reset };
}

// Custom hook for infinite scroll
function useInfiniteScroll(callback, hasMore) {
    const [isFetching, setIsFetching] = useState(false);
    
    useEffect(() => {
        window.addEventListener('scroll', handleScroll);
        return () => window.removeEventListener('scroll', handleScroll);
    }, []);
    
    useEffect(() => {
        if (!isFetching) return;
        if (!hasMore) return;
        
        callback();
    }, [isFetching, callback, hasMore]);
    
    function handleScroll() {
        if (window.innerHeight + document.documentElement.scrollTop !== document.documentElement.offsetHeight || isFetching) return;
        setIsFetching(true);
    }
    
    return [isFetching, setIsFetching];
}

// Usage
function UserList() {
    const { data: users, loading, error, hasMore, loadMore } = usePaginatedApi('/api/users');
    const [isFetching, setIsFetching] = useInfiniteScroll(loadMore, hasMore);
    
    useEffect(() => {
        if (isFetching) {
            loadMore();
            setIsFetching(false);
        }
    }, [isFetching, loadMore]);
    
    if (loading && users.length === 0) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <div>
            {users.map(user => (
                <div key={user.id}>{user.name}</div>
            ))}
            {loading && <div>Loading more...</div>}
        </div>
    );
}
```

## Advanced Custom Hooks

### Form Handling Hooks
```jsx
import { useState, useCallback } from 'react';

// Custom hook for form handling
function useForm(initialValues, validationRules = {}) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    const [isSubmitting, setIsSubmitting] = useState(false);
    
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
        return async (event) => {
            event.preventDefault();
            
            if (!validate()) {
                return;
            }
            
            setIsSubmitting(true);
            try {
                await onSubmit(values);
            } catch (error) {
                console.error('Form submission error:', error);
            } finally {
                setIsSubmitting(false);
            }
        };
    }, [values, validate]);
    
    const reset = useCallback(() => {
        setValues(initialValues);
        setErrors({});
        setTouched({});
        setIsSubmitting(false);
    }, [initialValues]);
    
    return {
        values,
        errors,
        touched,
        isSubmitting,
        setValue,
        setFieldTouched,
        validate,
        handleChange,
        handleBlur,
        handleSubmit,
        reset
    };
}

// Custom hook for field validation
function useFieldValidation(name, value, validationRule) {
    const [error, setError] = useState(null);
    const [touched, setTouched] = useState(false);
    
    useEffect(() => {
        if (touched && validationRule) {
            const validationError = validationRule(value);
            setError(validationError);
        }
    }, [value, touched, validationRule]);
    
    const handleBlur = useCallback(() => {
        setTouched(true);
    }, []);
    
    return { error, touched, handleBlur };
}

// Usage
function ContactForm() {
    const validationRules = {
        name: (value) => !value ? 'Name is required' : null,
        email: (value) => {
            if (!value) return 'Email is required';
            if (!/\S+@\S+\.\S+/.test(value)) return 'Email is invalid';
            return null;
        },
        message: (value) => !value ? 'Message is required' : null
    };
    
    const {
        values,
        errors,
        touched,
        isSubmitting,
        handleChange,
        handleBlur,
        handleSubmit,
        reset
    } = useForm({
        name: '',
        email: '',
        message: ''
    }, validationRules);
    
    const onSubmit = async (formValues) => {
        console.log('Form submitted:', formValues);
        // Handle form submission
        reset();
    };
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label htmlFor="name">Name</label>
                <input
                    id="name"
                    name="name"
                    value={values.name}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    placeholder="Enter your name"
                />
                {touched.name && errors.name && <span>{errors.name}</span>}
            </div>
            
            <div>
                <label htmlFor="email">Email</label>
                <input
                    id="email"
                    name="email"
                    type="email"
                    value={values.email}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    placeholder="Enter your email"
                />
                {touched.email && errors.email && <span>{errors.email}</span>}
            </div>
            
            <div>
                <label htmlFor="message">Message</label>
                <textarea
                    id="message"
                    name="message"
                    value={values.message}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    placeholder="Enter your message"
                />
                {touched.message && errors.message && <span>{errors.message}</span>}
            </div>
            
            <button type="submit" disabled={isSubmitting}>
                {isSubmitting ? 'Submitting...' : 'Submit'}
            </button>
        </form>
    );
}
```

### State Management Hooks
```jsx
import { useState, useCallback, useReducer } from 'react';

// Custom hook for complex state management
function useReducerState(initialState) {
    const [state, setState] = useState(initialState);
    
    const updateState = useCallback((updates) => {
        setState(prev => ({ ...prev, ...updates }));
    }, []);
    
    const resetState = useCallback(() => {
        setState(initialState);
    }, [initialState]);
    
    return [state, updateState, resetState];
}

// Custom hook for toggle state
function useToggle(initialValue = false) {
    const [value, setValue] = useState(initialValue);
    
    const toggle = useCallback(() => {
        setValue(prev => !prev);
    }, []);
    
    const setTrue = useCallback(() => {
        setValue(true);
    }, []);
    
    const setFalse = useCallback(() => {
        setValue(false);
    }, []);
    
    return [value, { toggle, setTrue, setFalse }];
}

// Custom hook for counter
function useCounter(initialValue = 0, step = 1) {
    const [count, setCount] = useState(initialValue);
    
    const increment = useCallback(() => {
        setCount(prev => prev + step);
    }, [step]);
    
    const decrement = useCallback(() => {
        setCount(prev => prev - step);
    }, [step]);
    
    const reset = useCallback(() => {
        setCount(initialValue);
    }, [initialValue]);
    
    const setValue = useCallback((value) => {
        setCount(value);
    }, []);
    
    return [count, { increment, decrement, reset, setValue }];
}

// Custom hook for list management
function useList(initialList = []) {
    const [list, setList] = useState(initialList);
    
    const addItem = useCallback((item) => {
        setList(prev => [...prev, item]);
    }, []);
    
    const removeItem = useCallback((index) => {
        setList(prev => prev.filter((_, i) => i !== index));
    }, []);
    
    const updateItem = useCallback((index, item) => {
        setList(prev => prev.map((existingItem, i) => 
            i === index ? item : existingItem
        ));
    }, []);
    
    const clearList = useCallback(() => {
        setList([]);
    }, []);
    
    const resetList = useCallback(() => {
        setList(initialList);
    }, [initialList]);
    
    return [list, { addItem, removeItem, updateItem, clearList, resetList }];
}

// Usage
function TodoApp() {
    const [todos, todoActions] = useList([]);
    const [isModalOpen, modalActions] = useToggle(false);
    const [count, counterActions] = useCounter(0);
    
    const addTodo = (text) => {
        todoActions.addItem({
            id: Date.now(),
            text,
            completed: false
        });
    };
    
    const toggleTodo = (id) => {
        const index = todos.findIndex(todo => todo.id === id);
        if (index !== -1) {
            const todo = todos[index];
            todoActions.updateItem(index, { ...todo, completed: !todo.completed });
        }
    };
    
    return (
        <div>
            <h1>Todo App (Count: {count})</h1>
            <button onClick={counterActions.increment}>Increment</button>
            <button onClick={counterActions.decrement}>Decrement</button>
            
            <button onClick={modalActions.toggle}>
                {isModalOpen ? 'Close' : 'Open'} Modal
            </button>
            
            {isModalOpen && (
                <div className="modal">
                    <h2>Add Todo</h2>
                    <form onSubmit={(e) => {
                        e.preventDefault();
                        const text = e.target.todoText.value;
                        addTodo(text);
                        e.target.reset();
                        modalActions.setFalse();
                    }}>
                        <input name="todoText" placeholder="Enter todo" />
                        <button type="submit">Add</button>
                    </form>
                </div>
            )}
            
            <ul>
                {todos.map((todo, index) => (
                    <li key={todo.id}>
                        <input
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => toggleTodo(todo.id)}
                        />
                        <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
                            {todo.text}
                        </span>
                        <button onClick={() => todoActions.removeItem(index)}>Delete</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

## Performance-Optimized Hooks

### Memoized Hooks
```jsx
import { useState, useEffect, useCallback, useMemo } from 'react';

// Custom hook with memoization
function useMemoizedApi(url, dependencies = []) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    const fetchData = useCallback(async () => {
        try {
            setLoading(true);
            setError(null);
            
            const response = await fetch(url);
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const result = await response.json();
            setData(result);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    }, [url]);
    
    useEffect(() => {
        fetchData();
    }, [fetchData, ...dependencies]);
    
    const memoizedData = useMemo(() => data, [data]);
    
    return { data: memoizedData, loading, error, refetch: fetchData };
}

// Custom hook for expensive calculations
function useExpensiveCalculation(data, calculationFn) {
    const [result, setResult] = useState(null);
    const [loading, setLoading] = useState(false);
    
    const memoizedResult = useMemo(() => {
        if (!data) return null;
        
        setLoading(true);
        const calculated = calculationFn(data);
        setLoading(false);
        
        return calculated;
    }, [data, calculationFn]);
    
    useEffect(() => {
        setResult(memoizedResult);
    }, [memoizedResult]);
    
    return { result, loading };
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

// Custom hook for lazy loading
function useLazyLoad(threshold = 0.1) {
    const [ref, isIntersecting] = useIntersectionObserver({ threshold });
    const [hasLoaded, setHasLoaded] = useState(false);
    
    useEffect(() => {
        if (isIntersecting && !hasLoaded) {
            setHasLoaded(true);
        }
    }, [isIntersecting, hasLoaded]);
    
    return [ref, hasLoaded];
}

// Usage
function LazyImage({ src, alt, ...props }) {
    const [ref, hasLoaded] = useLazyLoad();
    
    return (
        <div ref={ref} {...props}>
            {hasLoaded ? (
                <img src={src} alt={alt} />
            ) : (
                <div className="placeholder">Loading...</div>
            )}
        </div>
    );
}
```

## Exercise: Custom Hooks Library

Create a comprehensive custom hooks library with:
- Data fetching and caching hooks
- Form handling and validation hooks
- State management hooks
- Performance optimization hooks
- UI interaction hooks
- Utility hooks for common patterns

## Key Takeaways
- Custom hooks allow you to extract and reuse stateful logic
- Use useCallback and useMemo to optimize hook performance
- Create composable hooks that can be combined
- Follow the "use" naming convention for custom hooks
- Test custom hooks independently from components
- Document custom hooks with JSDoc comments
- Use TypeScript for better type safety in custom hooks
- Consider error handling and edge cases in custom hooks
- Create hooks that are flexible and configurable
- Use custom hooks to reduce code duplication and improve maintainability
