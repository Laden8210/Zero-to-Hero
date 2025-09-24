# Context and State Management

## Introduction
React Context and advanced state management patterns are essential for building scalable applications. This lesson covers Context API, state management libraries, and patterns for complex state handling.

## React Context API

### Basic Context Usage
```jsx
import React, { createContext, useContext, useReducer } from 'react';

// Create context
const ThemeContext = createContext();

// Theme provider component
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prev => prev === 'light' ? 'dark' : 'light');
    };
    
    const value = {
        theme,
        toggleTheme,
        isDark: theme === 'dark'
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

// Theme-aware component
function ThemedButton({ children, ...props }) {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <button
            {...props}
            className={`btn btn-${theme}`}
            onClick={toggleTheme}
        >
            {children}
        </button>
    );
}

// Usage
function App() {
    return (
        <ThemeProvider>
            <div className="app">
                <ThemedButton>Toggle Theme</ThemedButton>
            </div>
        </ThemeProvider>
    );
}
```

### Complex Context with useReducer
```jsx
import React, { createContext, useContext, useReducer } from 'react';

// Action types
const ActionTypes = {
    ADD_TODO: 'ADD_TODO',
    REMOVE_TODO: 'REMOVE_TODO',
    TOGGLE_TODO: 'TOGGLE_TODO',
    SET_FILTER: 'SET_FILTER',
    CLEAR_COMPLETED: 'CLEAR_COMPLETED'
};

// Initial state
const initialState = {
    todos: [],
    filter: 'all' // 'all', 'active', 'completed'
};

// Reducer function
function todoReducer(state, action) {
    switch (action.type) {
        case ActionTypes.ADD_TODO:
            return {
                ...state,
                todos: [...state.todos, {
                    id: Date.now(),
                    text: action.payload,
                    completed: false
                }]
            };
            
        case ActionTypes.REMOVE_TODO:
            return {
                ...state,
                todos: state.todos.filter(todo => todo.id !== action.payload)
            };
            
        case ActionTypes.TOGGLE_TODO:
            return {
                ...state,
                todos: state.todos.map(todo =>
                    todo.id === action.payload
                        ? { ...todo, completed: !todo.completed }
                        : todo
                )
            };
            
        case ActionTypes.SET_FILTER:
            return {
                ...state,
                filter: action.payload
            };
            
        case ActionTypes.CLEAR_COMPLETED:
            return {
                ...state,
                todos: state.todos.filter(todo => !todo.completed)
            };
            
        default:
            return state;
    }
}

// Create context
const TodoContext = createContext();

// Todo provider component
function TodoProvider({ children }) {
    const [state, dispatch] = useReducer(todoReducer, initialState);
    
    const actions = {
        addTodo: (text) => dispatch({ type: ActionTypes.ADD_TODO, payload: text }),
        removeTodo: (id) => dispatch({ type: ActionTypes.REMOVE_TODO, payload: id }),
        toggleTodo: (id) => dispatch({ type: ActionTypes.TOGGLE_TODO, payload: id }),
        setFilter: (filter) => dispatch({ type: ActionTypes.SET_FILTER, payload: filter }),
        clearCompleted: () => dispatch({ type: ActionTypes.CLEAR_COMPLETED })
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
    
    const value = {
        ...state,
        filteredTodos,
        ...actions
    };
    
    return (
        <TodoContext.Provider value={value}>
            {children}
        </TodoContext.Provider>
    );
}

// Custom hook to use todo context
function useTodos() {
    const context = useContext(TodoContext);
    if (!context) {
        throw new Error('useTodos must be used within a TodoProvider');
    }
    return context;
}

// Todo components
function TodoList() {
    const { filteredTodos, toggleTodo, removeTodo } = useTodos();
    
    return (
        <ul>
            {filteredTodos.map(todo => (
                <li key={todo.id}>
                    <input
                        type="checkbox"
                        checked={todo.completed}
                        onChange={() => toggleTodo(todo.id)}
                    />
                    <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
                        {todo.text}
                    </span>
                    <button onClick={() => removeTodo(todo.id)}>Delete</button>
                </li>
            ))}
        </ul>
    );
}

function TodoForm() {
    const { addTodo } = useTodos();
    const [text, setText] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        if (text.trim()) {
            addTodo(text.trim());
            setText('');
        }
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                value={text}
                onChange={(e) => setText(e.target.value)}
                placeholder="Add a todo..."
            />
            <button type="submit">Add</button>
        </form>
    );
}

function TodoFilter() {
    const { filter, setFilter } = useTodos();
    
    return (
        <div>
            <button
                className={filter === 'all' ? 'active' : ''}
                onClick={() => setFilter('all')}
            >
                All
            </button>
            <button
                className={filter === 'active' ? 'active' : ''}
                onClick={() => setFilter('active')}
            >
                Active
            </button>
            <button
                className={filter === 'completed' ? 'active' : ''}
                onClick={() => setFilter('completed')}
            >
                Completed
            </button>
        </div>
    );
}

// Usage
function TodoApp() {
    return (
        <TodoProvider>
            <div className="todo-app">
                <h1>Todo App</h1>
                <TodoForm />
                <TodoFilter />
                <TodoList />
            </div>
        </TodoProvider>
    );
}
```

## Advanced State Management Patterns

### State Machine Pattern
```jsx
import React, { useReducer, useCallback } from 'react';

// State machine for async operations
const asyncStates = {
    IDLE: 'idle',
    LOADING: 'loading',
    SUCCESS: 'success',
    ERROR: 'error'
};

const asyncActions = {
    START: 'start',
    SUCCESS: 'success',
    ERROR: 'error',
    RESET: 'reset'
};

function asyncReducer(state, action) {
    switch (action.type) {
        case asyncActions.START:
            return {
                ...state,
                status: asyncStates.LOADING,
                error: null
            };
            
        case asyncActions.SUCCESS:
            return {
                ...state,
                status: asyncStates.SUCCESS,
                data: action.payload,
                error: null
            };
            
        case asyncActions.ERROR:
            return {
                ...state,
                status: asyncStates.ERROR,
                error: action.payload
            };
            
        case asyncActions.RESET:
            return {
                ...state,
                status: asyncStates.IDLE,
                data: null,
                error: null
            };
            
        default:
            return state;
    }
}

// Custom hook for async operations
function useAsync(asyncFunction, immediate = false) {
    const [state, dispatch] = useReducer(asyncReducer, {
        status: asyncStates.IDLE,
        data: null,
        error: null
    });
    
    const execute = useCallback(async (...args) => {
        dispatch({ type: asyncActions.START });
        
        try {
            const result = await asyncFunction(...args);
            dispatch({ type: asyncActions.SUCCESS, payload: result });
            return result;
        } catch (error) {
            dispatch({ type: asyncActions.ERROR, payload: error.message });
            throw error;
        }
    }, [asyncFunction]);
    
    const reset = useCallback(() => {
        dispatch({ type: asyncActions.RESET });
    }, []);
    
    React.useEffect(() => {
        if (immediate) {
            execute();
        }
    }, [execute, immediate]);
    
    return {
        ...state,
        execute,
        reset,
        isLoading: state.status === asyncStates.LOADING,
        isSuccess: state.status === asyncStates.SUCCESS,
        isError: state.status === asyncStates.ERROR,
        isIdle: state.status === asyncStates.IDLE
    };
}

// Usage
function UserProfile({ userId }) {
    const fetchUser = useCallback(async (id) => {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) {
            throw new Error('Failed to fetch user');
        }
        return response.json();
    }, []);
    
    const { data: user, isLoading, isError, error, execute } = useAsync(
        () => fetchUser(userId),
        true
    );
    
    if (isLoading) return <div>Loading...</div>;
    if (isError) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
            <button onClick={() => execute()}>Refresh</button>
        </div>
    );
}
```

### Compound Component Pattern
```jsx
import React, { createContext, useContext, useState } from 'react';

// Context for compound component
const AccordionContext = createContext();

// Main accordion component
function Accordion({ children, allowMultiple = false }) {
    const [openItems, setOpenItems] = useState(new Set());
    
    const toggleItem = (itemId) => {
        setOpenItems(prev => {
            const newSet = new Set(prev);
            if (newSet.has(itemId)) {
                newSet.delete(itemId);
            } else {
                if (!allowMultiple) {
                    newSet.clear();
                }
                newSet.add(itemId);
            }
            return newSet;
        });
    };
    
    const isItemOpen = (itemId) => openItems.has(itemId);
    
    const value = {
        toggleItem,
        isItemOpen,
        allowMultiple
    };
    
    return (
        <AccordionContext.Provider value={value}>
            <div className="accordion">
                {children}
            </div>
        </AccordionContext.Provider>
    );
}

// Custom hook for accordion context
function useAccordion() {
    const context = useContext(AccordionContext);
    if (!context) {
        throw new Error('Accordion components must be used within Accordion');
    }
    return context;
}

// Accordion item component
function AccordionItem({ children, id }) {
    const { isItemOpen } = useAccordion();
    const isOpen = isItemOpen(id);
    
    return (
        <div className={`accordion-item ${isOpen ? 'open' : ''}`}>
            {React.Children.map(children, child =>
                React.cloneElement(child, { itemId: id, isOpen })
            )}
        </div>
    );
}

// Accordion header component
function AccordionHeader({ children, itemId, isOpen }) {
    const { toggleItem } = useAccordion();
    
    return (
        <div
            className="accordion-header"
            onClick={() => toggleItem(itemId)}
        >
            {children}
            <span className={`accordion-icon ${isOpen ? 'open' : ''}`}>
                ▼
            </span>
        </div>
    );
}

// Accordion content component
function AccordionContent({ children, itemId, isOpen }) {
    return (
        <div className={`accordion-content ${isOpen ? 'open' : ''}`}>
            {isOpen && children}
        </div>
    );
}

// Usage
function AccordionExample() {
    return (
        <Accordion allowMultiple={true}>
            <AccordionItem id="item1">
                <AccordionHeader>
                    <h3>Section 1</h3>
                </AccordionHeader>
                <AccordionContent>
                    <p>Content for section 1</p>
                </AccordionContent>
            </AccordionItem>
            
            <AccordionItem id="item2">
                <AccordionHeader>
                    <h3>Section 2</h3>
                </AccordionHeader>
                <AccordionContent>
                    <p>Content for section 2</p>
                </AccordionContent>
            </AccordionItem>
            
            <AccordionItem id="item3">
                <AccordionHeader>
                    <h3>Section 3</h3>
                </AccordionHeader>
                <AccordionContent>
                    <p>Content for section 3</p>
                </AccordionContent>
            </AccordionItem>
        </Accordion>
    );
}
```

## State Management Libraries Integration

### Zustand Integration
```jsx
import { create } from 'zustand';
import { devtools, persist } from 'zustand/middleware';

// Zustand store
const useStore = create(
    devtools(
        persist(
            (set, get) => ({
                // State
                user: null,
                todos: [],
                filter: 'all',
                
                // Actions
                setUser: (user) => set({ user }),
                addTodo: (text) => set((state) => ({
                    todos: [...state.todos, {
                        id: Date.now(),
                        text,
                        completed: false
                    }]
                })),
                toggleTodo: (id) => set((state) => ({
                    todos: state.todos.map(todo =>
                        todo.id === id
                            ? { ...todo, completed: !todo.completed }
                            : todo
                    )
                })),
                removeTodo: (id) => set((state) => ({
                    todos: state.todos.filter(todo => todo.id !== id)
                })),
                setFilter: (filter) => set({ filter }),
                clearCompleted: () => set((state) => ({
                    todos: state.todos.filter(todo => !todo.completed)
                })),
                
                // Computed values
                getFilteredTodos: () => {
                    const { todos, filter } = get();
                    switch (filter) {
                        case 'active':
                            return todos.filter(todo => !todo.completed);
                        case 'completed':
                            return todos.filter(todo => todo.completed);
                        default:
                            return todos;
                    }
                },
                
                getTodoStats: () => {
                    const { todos } = get();
                    return {
                        total: todos.length,
                        completed: todos.filter(todo => todo.completed).length,
                        active: todos.filter(todo => !todo.completed).length
                    };
                }
            }),
            {
                name: 'todo-storage',
                partialize: (state) => ({
                    todos: state.todos,
                    filter: state.filter
                })
            }
        ),
        {
            name: 'todo-store'
        }
    )
);

// Custom hooks for specific parts of state
function useTodos() {
    return useStore((state) => ({
        todos: state.getFilteredTodos(),
        addTodo: state.addTodo,
        toggleTodo: state.toggleTodo,
        removeTodo: state.removeTodo,
        clearCompleted: state.clearCompleted
    }));
}

function useTodoFilter() {
    return useStore((state) => ({
        filter: state.filter,
        setFilter: state.setFilter,
        stats: state.getTodoStats()
    }));
}

function useUser() {
    return useStore((state) => ({
        user: state.user,
        setUser: state.setUser
    }));
}

// Components using Zustand
function TodoList() {
    const { todos, toggleTodo, removeTodo } = useTodos();
    
    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id}>
                    <input
                        type="checkbox"
                        checked={todo.completed}
                        onChange={() => toggleTodo(todo.id)}
                    />
                    <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
                        {todo.text}
                    </span>
                    <button onClick={() => removeTodo(todo.id)}>Delete</button>
                </li>
            ))}
        </ul>
    );
}

function TodoForm() {
    const { addTodo } = useTodos();
    const [text, setText] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        if (text.trim()) {
            addTodo(text.trim());
            setText('');
        }
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                value={text}
                onChange={(e) => setText(e.target.value)}
                placeholder="Add a todo..."
            />
            <button type="submit">Add</button>
        </form>
    );
}

function TodoFilter() {
    const { filter, setFilter, stats } = useTodoFilter();
    
    return (
        <div>
            <div>
                <button
                    className={filter === 'all' ? 'active' : ''}
                    onClick={() => setFilter('all')}
                >
                    All ({stats.total})
                </button>
                <button
                    className={filter === 'active' ? 'active' : ''}
                    onClick={() => setFilter('active')}
                >
                    Active ({stats.active})
                </button>
                <button
                    className={filter === 'completed' ? 'active' : ''}
                    onClick={() => setFilter('completed')}
                >
                    Completed ({stats.completed})
                </button>
            </div>
        </div>
    );
}

// Usage
function TodoApp() {
    return (
        <div className="todo-app">
            <h1>Todo App</h1>
            <TodoForm />
            <TodoFilter />
            <TodoList />
        </div>
    );
}
```

## Exercise: State Management System

Create a comprehensive state management system with:
- React Context for component communication
- useReducer for complex state logic
- Custom hooks for state management
- State machine patterns for async operations
- Compound component patterns
- Integration with external state management libraries

## Key Takeaways
- Use React Context for sharing state across component trees
- Combine useReducer with Context for complex state management
- Create custom hooks to encapsulate state logic
- Use state machines for predictable state transitions
- Implement compound components for flexible UI patterns
- Consider external libraries like Zustand for complex applications
- Optimize Context performance by splitting contexts
- Use TypeScript for better type safety in state management
- Test state management logic independently
- Document state management patterns for team consistency
