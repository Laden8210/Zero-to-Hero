# State Management in React

## Introduction
State management is crucial for building interactive React applications. This lesson covers React's built-in state management solutions: useState, useEffect, useContext, and useReducer.

## useState Hook Advanced Usage

### Basic State Management
```jsx
import { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
            <button onClick={() => setCount(count - 1)}>
                Decrement
            </button>
            <button onClick={() => setCount(0)}>
                Reset
            </button>
        </div>
    );
}
```

**Code Explanation:**
- `import { useState } from 'react'`: Imports the useState hook from React
- `const [count, setCount] = useState(0)`: Creates state variable `count` with initial value 0 and setter function `setCount`
- `useState(0)`: Hook that returns an array with current state value and state setter function
- `{count}`: JSX expression that displays the current count value
- `onClick={() => setCount(count + 1)}`: Event handler that updates state when button is clicked
- `setCount(count + 1)`: Updates state by incrementing current count by 1
- `setCount(count - 1)`: Updates state by decrementing current count by 1
- `setCount(0)`: Resets state back to initial value

**Benefits:**
- useState provides simple state management for functional components
- State updates trigger component re-renders automatically
- State persists between re-renders
- Easy to understand and implement for simple use cases
- No need for class components or complex state management libraries

### Functional Updates and State Dependencies
```jsx
function AdvancedCounter() {
    const [count, setCount] = useState(0);
    const [step, setStep] = useState(1);
    
    // Functional updates for state depending on previous state
    const increment = () => {
        setCount(prevCount => prevCount + step);
    };
    
    const decrement = () => {
        setCount(prevCount => prevCount - step);
    };
    
    // Multiple state updates in one function
    const reset = () => {
        setCount(0);
        setStep(1);
    };
    
    return (
        <div>
            <p>Count: {count}</p>
            <p>Step: {step}</p>
            
            <div>
                <button onClick={increment}>+{step}</button>
                <button onClick={decrement}>-{step}</button>
            </div>
            
            <div>
                <button onClick={() => setStep(1)}>Step 1</button>
                <button onClick={() => setStep(5)}>Step 5</button>
                <button onClick={() => setStep(10)}>Step 10</button>
            </div>
            
            <button onClick={reset}>Reset All</button>
        </div>
    );
}
```

### Complex State Objects
```jsx
function UserProfile() {
    const [user, setUser] = useState({
        name: '',
        email: '',
        age: 0,
        preferences: {
            theme: 'light',
            notifications: true
        }
    });
    
    // Updating nested state objects
    const updateUser = (field, value) => {
        setUser(prevUser => ({
            ...prevUser,
            [field]: value
        }));
    };
    
    const updatePreferences = (prefField, value) => {
        setUser(prevUser => ({
            ...prevUser,
            preferences: {
                ...prevUser.preferences,
                [prefField]: value
            }
        }));
    };
    
    return (
        <div>
            <div>
                <label>Name:</label>
                <input 
                    value={user.name}
                    onChange={(e) => updateUser('name', e.target.value)}
                />
            </div>
            
            <div>
                <label>Email:</label>
                <input 
                    type="email"
                    value={user.email}
                    onChange={(e) => updateUser('email', e.target.value)}
                />
            </div>
            
            <div>
                <label>Age:</label>
                <input 
                    type="number"
                    value={user.age}
                    onChange={(e) => updateUser('age', parseInt(e.target.value))}
                />
            </div>
            
            <div>
                <label>Theme:</label>
                <select 
                    value={user.preferences.theme}
                    onChange={(e) => updatePreferences('theme', e.target.value)}
                >
                    <option value="light">Light</option>
                    <option value="dark">Dark</option>
                </select>
            </div>
            
            <div>
                <label>
                    <input 
                        type="checkbox"
                        checked={user.preferences.notifications}
                        onChange={(e) => updatePreferences('notifications', e.target.checked)}
                    />
                    Enable Notifications
                </label>
            </div>
        </div>
    );
}
```

## useEffect Hook Lifecycle Management

### Basic useEffect Patterns
```jsx
import { useState, useEffect } from 'react';

function DataFetcher({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        // Effect runs after every render by default
        console.log('Effect running');
        
        // Cleanup function
        return () => {
            console.log('Effect cleanup');
        };
    });
    
    return <div>Component renders</div>;
}
```

### Dependency Array Patterns
```jsx
function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    
    // Effect runs only on mount and unmount
    useEffect(() => {
        console.log('Component mounted');
        
        return () => {
            console.log('Component unmounted');
        };
    }, []); // Empty dependency array
    
    // Effect runs when userId changes
    useEffect(() => {
        if (userId) {
            setLoading(true);
            fetchUser(userId)
                .then(userData => {
                    setUser(userData);
                    setLoading(false);
                })
                .catch(error => {
                    console.error('Error fetching user:', error);
                    setLoading(false);
                });
        }
    }, [userId]); // Dependency on userId
    
    // Effect runs on every render (no dependency array)
    useEffect(() => {
        document.title = user ? `${user.name} - Profile` : 'Loading...';
    }); // No dependency array
    
    if (loading) return <div>Loading...</div>;
    if (!user) return <div>User not found</div>;
    
    return (
        <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
        </div>
    );
}
```

### Cleanup Functions and Memory Leaks Prevention
```jsx
function Timer() {
    const [seconds, setSeconds] = useState(0);
    const [isRunning, setIsRunning] = useState(false);
    
    useEffect(() => {
        let intervalId;
        
        if (isRunning) {
            intervalId = setInterval(() => {
                setSeconds(prevSeconds => prevSeconds + 1);
            }, 1000);
        }
        
        // Cleanup function to prevent memory leaks
        return () => {
            if (intervalId) {
                clearInterval(intervalId);
            }
        };
    }, [isRunning]);
    
    return (
        <div>
            <p>Timer: {seconds} seconds</p>
            <button onClick={() => setIsRunning(!isRunning)}>
                {isRunning ? 'Pause' : 'Start'}
            </button>
            <button onClick={() => setSeconds(0)}>Reset</button>
        </div>
    );
}

// Event listener cleanup
function WindowSizeTracker() {
    const [windowSize, setWindowSize] = useState({
        width: window.innerWidth,
        height: window.innerHeight
    });
    
    useEffect(() => {
        const handleResize = () => {
            setWindowSize({
                width: window.innerWidth,
                height: window.innerHeight
            });
        };
        
        window.addEventListener('resize', handleResize);
        
        // Cleanup function
        return () => {
            window.removeEventListener('resize', handleResize);
        };
    }, []); // Empty dependency array - only run on mount/unmount
    
    return (
        <div>
            <p>Window size: {windowSize.width} x {windowSize.height}</p>
        </div>
    );
}
```

## useContext for Global State

### Creating and Using Context
```jsx
import { createContext, useContext, useState } from 'react';

// Create context
const ThemeContext = createContext();

// Context provider component
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
    };
    
    const value = {
        theme,
        toggleTheme
    };
    
    return (
        <ThemeContext.Provider value={value}>
            {children}
        </ThemeContext.Provider>
    );
}

// Custom hook to use theme context
function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within a ThemeProvider');
    }
    return context;
}

// Components using the context
function Header() {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <header className={`header ${theme}`}>
            <h1>My App</h1>
            <button onClick={toggleTheme}>
                Switch to {theme === 'light' ? 'dark' : 'light'} theme
            </button>
        </header>
    );
}

function Main() {
    const { theme } = useTheme();
    
    return (
        <main className={`main ${theme}`}>
            <p>This is the main content area.</p>
        </main>
    );
}

// App component with provider
function App() {
    return (
        <ThemeProvider>
            <div className="app">
                <Header />
                <Main />
            </div>
        </ThemeProvider>
    );
}
```

### Multiple Contexts Pattern
```jsx
// User Context
const UserContext = createContext();

function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        // Simulate fetching user data
        setTimeout(() => {
            setUser({ id: 1, name: 'John Doe', email: 'john@example.com' });
            setLoading(false);
        }, 1000);
    }, []);
    
    const login = (userData) => {
        setUser(userData);
    };
    
    const logout = () => {
        setUser(null);
    };
    
    return (
        <UserContext.Provider value={{ user, loading, login, logout }}>
            {children}
        </UserContext.Provider>
    );
}

// Notification Context
const NotificationContext = createContext();

function NotificationProvider({ children }) {
    const [notifications, setNotifications] = useState([]);
    
    const addNotification = (message, type = 'info') => {
        const id = Date.now();
        const notification = { id, message, type };
        setNotifications(prev => [...prev, notification]);
        
        // Auto-remove after 3 seconds
        setTimeout(() => {
            removeNotification(id);
        }, 3000);
    };
    
    const removeNotification = (id) => {
        setNotifications(prev => prev.filter(n => n.id !== id));
    };
    
    return (
        <NotificationContext.Provider value={{ 
            notifications, 
            addNotification, 
            removeNotification 
        }}>
            {children}
        </NotificationContext.Provider>
    );
}

// Custom hooks
function useUser() {
    const context = useContext(UserContext);
    if (!context) {
        throw new Error('useUser must be used within a UserProvider');
    }
    return context;
}

function useNotifications() {
    const context = useContext(NotificationContext);
    if (!context) {
        throw new Error('useNotifications must be used within a NotificationProvider');
    }
    return context;
}

// Component using multiple contexts
function UserDashboard() {
    const { user, logout } = useUser();
    const { addNotification } = useNotifications();
    
    const handleLogout = () => {
        logout();
        addNotification('Successfully logged out', 'success');
    };
    
    return (
        <div>
            <h2>Welcome, {user?.name}!</h2>
            <button onClick={handleLogout}>Logout</button>
        </div>
    );
}
```

## useReducer for Complex State Logic

### Basic useReducer Pattern
```jsx
import { useReducer } from 'react';

// Reducer function
function counterReducer(state, action) {
    switch (action.type) {
        case 'increment':
            return { count: state.count + 1 };
        case 'decrement':
            return { count: state.count - 1 };
        case 'reset':
            return { count: 0 };
        case 'set':
            return { count: action.payload };
        default:
            throw new Error(`Unhandled action type: ${action.type}`);
    }
}

function Counter() {
    const [state, dispatch] = useReducer(counterReducer, { count: 0 });
    
    return (
        <div>
            <p>Count: {state.count}</p>
            <button onClick={() => dispatch({ type: 'increment' })}>
                Increment
            </button>
            <button onClick={() => dispatch({ type: 'decrement' })}>
                Decrement
            </button>
            <button onClick={() => dispatch({ type: 'reset' })}>
                Reset
            </button>
            <button onClick={() => dispatch({ type: 'set', payload: 10 })}>
                Set to 10
            </button>
        </div>
    );
}
```

### Complex State with useReducer
```jsx
// Todo reducer
function todoReducer(state, action) {
    switch (action.type) {
        case 'ADD_TODO':
            return {
                ...state,
                todos: [...state.todos, {
                    id: Date.now(),
                    text: action.payload,
                    completed: false
                }]
            };
        
        case 'TOGGLE_TODO':
            return {
                ...state,
                todos: state.todos.map(todo =>
                    todo.id === action.payload
                        ? { ...todo, completed: !todo.completed }
                        : todo
                )
            };
        
        case 'DELETE_TODO':
            return {
                ...state,
                todos: state.todos.filter(todo => todo.id !== action.payload)
            };
        
        case 'SET_FILTER':
            return {
                ...state,
                filter: action.payload
            };
        
        case 'CLEAR_COMPLETED':
            return {
                ...state,
                todos: state.todos.filter(todo => !todo.completed)
            };
        
        default:
            throw new Error(`Unhandled action type: ${action.type}`);
    }
}

function TodoApp() {
    const [state, dispatch] = useReducer(todoReducer, {
        todos: [],
        filter: 'all'
    });
    
    const [inputValue, setInputValue] = useState('');
    
    const addTodo = () => {
        if (inputValue.trim()) {
            dispatch({ type: 'ADD_TODO', payload: inputValue });
            setInputValue('');
        }
    };
    
    const filteredTodos = state.todos.filter(todo => {
        switch (state.filter) {
            case 'active':
                return !todo.completed;
            case 'completed':
                return todo.completed;
            default:
                return true;
        }
    });
    
    return (
        <div>
            <div>
                <input
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && addTodo()}
                    placeholder="Add a todo..."
                />
                <button onClick={addTodo}>Add</button>
            </div>
            
            <div>
                <button 
                    onClick={() => dispatch({ type: 'SET_FILTER', payload: 'all' })}
                    className={state.filter === 'all' ? 'active' : ''}
                >
                    All
                </button>
                <button 
                    onClick={() => dispatch({ type: 'SET_FILTER', payload: 'active' })}
                    className={state.filter === 'active' ? 'active' : ''}
                >
                    Active
                </button>
                <button 
                    onClick={() => dispatch({ type: 'SET_FILTER', payload: 'completed' })}
                    className={state.filter === 'completed' ? 'active' : ''}
                >
                    Completed
                </button>
            </div>
            
            <ul>
                {filteredTodos.map(todo => (
                    <li key={todo.id}>
                        <input
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
                        />
                        <span className={todo.completed ? 'completed' : ''}>
                            {todo.text}
                        </span>
                        <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>
                            Delete
                        </button>
                    </li>
                ))}
            </ul>
            
            <button onClick={() => dispatch({ type: 'CLEAR_COMPLETED' })}>
                Clear Completed
            </button>
        </div>
    );
}
```

## Exercise: State Management Application

Create a comprehensive state management application with:
- User authentication state with useContext
- Todo management with useReducer
- Real-time updates with useEffect
- Form state management with useState
- Error handling and loading states

## Key Takeaways
- useState is perfect for simple state management
- useEffect handles side effects and lifecycle events
- useContext provides global state without prop drilling
- useReducer is ideal for complex state logic
- Always provide cleanup functions in useEffect
- Use functional updates when state depends on previous state
- Context should be used sparingly to avoid performance issues
- Reducers make state transitions predictable and testable
