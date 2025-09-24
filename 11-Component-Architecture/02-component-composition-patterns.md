# Component Composition Patterns

## Introduction
Component composition is a powerful pattern in React that allows building complex UIs from simple, reusable components. This lesson covers composition patterns, higher-order components, render props, and compound components.

## Basic Composition Patterns

### Container and Presentational Components
```jsx
// Presentational Component (Dumb Component)
function UserList({ users, onUserSelect, onUserDelete }) {
    return (
        <div className="user-list">
            {users.map(user => (
                <UserCard
                    key={user.id}
                    user={user}
                    onSelect={() => onUserSelect(user)}
                    onDelete={() => onUserDelete(user.id)}
                />
            ))}
        </div>
    );
}

function UserCard({ user, onSelect, onDelete }) {
    return (
        <div className="user-card">
            <h3>{user.name}</h3>
            <p>{user.email}</p>
            <div className="actions">
                <button onClick={onSelect}>View</button>
                <button onClick={onDelete}>Delete</button>
            </div>
        </div>
    );
}

// Container Component (Smart Component)
function UserListContainer() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        fetchUsers();
    }, []);
    
    const fetchUsers = async () => {
        try {
            setLoading(true);
            const response = await fetch('/api/users');
            const data = await response.json();
            setUsers(data);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    const handleUserSelect = (user) => {
        console.log('Selected user:', user);
        // Navigate to user detail page
    };
    
    const handleUserDelete = async (userId) => {
        try {
            await fetch(`/api/users/${userId}`, { method: 'DELETE' });
            setUsers(users.filter(user => user.id !== userId));
        } catch (err) {
            setError(err.message);
        }
    };
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <UserList
            users={users}
            onUserSelect={handleUserSelect}
            onUserDelete={handleUserDelete}
        />
    );
}

// Usage
function App() {
    return <UserListContainer />;
}
```

### Higher-Order Components (HOCs)
```jsx
// HOC for data fetching
function withData(WrappedComponent, dataSource) {
    return function DataComponent(props) {
        const [data, setData] = useState(null);
        const [loading, setLoading] = useState(true);
        const [error, setError] = useState(null);
        
        useEffect(() => {
            fetchData();
        }, []);
        
        const fetchData = async () => {
            try {
                setLoading(true);
                const response = await fetch(dataSource);
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        };
        
        if (loading) return <div>Loading...</div>;
        if (error) return <div>Error: {error}</div>;
        
        return <WrappedComponent data={data} {...props} />;
    };
}

// HOC for authentication
function withAuth(WrappedComponent) {
    return function AuthComponent(props) {
        const [isAuthenticated, setIsAuthenticated] = useState(false);
        const [user, setUser] = useState(null);
        const [loading, setLoading] = useState(true);
        
        useEffect(() => {
            checkAuth();
        }, []);
        
        const checkAuth = async () => {
            try {
                const token = localStorage.getItem('token');
                if (token) {
                    const response = await fetch('/api/me', {
                        headers: { Authorization: `Bearer ${token}` }
                    });
                    if (response.ok) {
                        const userData = await response.json();
                        setUser(userData);
                        setIsAuthenticated(true);
                    }
                }
            } catch (error) {
                console.error('Auth check failed:', error);
            } finally {
                setLoading(false);
            }
        };
        
        if (loading) return <div>Loading...</div>;
        if (!isAuthenticated) return <div>Please log in</div>;
        
        return <WrappedComponent user={user} {...props} />;
    };
}

// HOC for error handling
function withErrorBoundary(WrappedComponent) {
    return class ErrorBoundaryComponent extends React.Component {
        constructor(props) {
            super(props);
            this.state = { hasError: false, error: null };
        }
        
        static getDerivedStateFromError(error) {
            return { hasError: true, error };
        }
        
        componentDidCatch(error, errorInfo) {
            console.error('Error caught by boundary:', error, errorInfo);
        }
        
        render() {
            if (this.state.hasError) {
                return (
                    <div className="error-boundary">
                        <h2>Something went wrong</h2>
                        <p>{this.state.error?.message}</p>
                        <button onClick={() => this.setState({ hasError: false, error: null })}>
                            Try again
                        </button>
                    </div>
                );
            }
            
            return <WrappedComponent {...this.props} />;
        }
    };
}

// Usage
const UserListWithData = withData(UserList, '/api/users');
const UserListWithAuth = withAuth(UserListWithData);
const UserListWithErrorBoundary = withErrorBoundary(UserListWithAuth);

// Composed HOCs
const EnhancedUserList = withErrorBoundary(withAuth(withData(UserList, '/api/users')));
```

### Render Props Pattern
```jsx
// Data fetching render prop
function DataFetcher({ url, render }) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    
    useEffect(() => {
        fetchData();
    }, [url]);
    
    const fetchData = async () => {
        try {
            setLoading(true);
            const response = await fetch(url);
            const result = await response.json();
            setData(result);
        } catch (err) {
            setError(err.message);
        } finally {
            setLoading(false);
        }
    };
    
    return render({ data, loading, error, refetch: fetchData });
}

// Mouse position render prop
function MouseTracker({ render }) {
    const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
    
    useEffect(() => {
        const handleMouseMove = (e) => {
            setMousePosition({ x: e.clientX, y: e.clientY });
        };
        
        window.addEventListener('mousemove', handleMouseMove);
        
        return () => {
            window.removeEventListener('mousemove', handleMouseMove);
        };
    }, []);
    
    return render(mousePosition);
}

// Local storage render prop
function LocalStorage({ key, render }) {
    const [value, setValue] = useState(() => {
        try {
            return JSON.parse(localStorage.getItem(key));
        } catch {
            return null;
        }
    });
    
    const setStoredValue = (newValue) => {
        setValue(newValue);
        localStorage.setItem(key, JSON.stringify(newValue));
    };
    
    return render({ value, setValue: setStoredValue });
}

// Usage
function App() {
    return (
        <div>
            <DataFetcher
                url="/api/users"
                render={({ data, loading, error }) => {
                    if (loading) return <div>Loading...</div>;
                    if (error) return <div>Error: {error}</div>;
                    return <UserList users={data} />;
                }}
            />
            
            <MouseTracker
                render={({ x, y }) => (
                    <div>
                        Mouse position: {x}, {y}
                    </div>
                )}
            />
            
            <LocalStorage
                key="userPreferences"
                render={({ value, setValue }) => (
                    <div>
                        <h3>User Preferences</h3>
                        <input
                            type="checkbox"
                            checked={value?.darkMode || false}
                            onChange={(e) => setValue({ ...value, darkMode: e.target.checked })}
                        />
                        Dark Mode
                    </div>
                )}
            />
        </div>
    );
}
```

## Advanced Composition Patterns

### Compound Components
```jsx
// Compound component pattern
const Tabs = ({ children, defaultTab }) => {
    const [activeTab, setActiveTab] = useState(defaultTab);
    
    return (
        <div className="tabs">
            {React.Children.map(children, child => {
                if (child.type === TabsList) {
                    return React.cloneElement(child, { activeTab, setActiveTab });
                }
                if (child.type === TabsContent) {
                    return React.cloneElement(child, { activeTab });
                }
                return child;
            })}
        </div>
    );
};

const TabsList = ({ activeTab, setActiveTab, children }) => {
    return (
        <div className="tabs-list">
            {React.Children.map(children, child => {
                if (child.type === Tab) {
                    return React.cloneElement(child, { 
                        isActive: child.props.value === activeTab,
                        onClick: () => setActiveTab(child.props.value)
                    });
                }
                return child;
            })}
        </div>
    );
};

const Tab = ({ children, isActive, onClick }) => {
    return (
        <button
            className={`tab ${isActive ? 'active' : ''}`}
            onClick={onClick}
        >
            {children}
        </button>
    );
};

const TabsContent = ({ activeTab, children }) => {
    return (
        <div className="tabs-content">
            {React.Children.map(children, child => {
                if (child.type === TabPanel) {
                    return React.cloneElement(child, { 
                        isActive: child.props.value === activeTab
                    });
                }
                return child;
            })}
        </div>
    );
};

const TabPanel = ({ children, isActive }) => {
    return isActive ? (
        <div className="tab-panel">
            {children}
        </div>
    ) : null;
};

// Usage
function App() {
    return (
        <Tabs defaultTab="tab1">
            <TabsList>
                <Tab value="tab1">Tab 1</Tab>
                <Tab value="tab2">Tab 2</Tab>
                <Tab value="tab3">Tab 3</Tab>
            </TabsList>
            
            <TabsContent>
                <TabPanel value="tab1">
                    <h3>Content for Tab 1</h3>
                    <p>This is the content for the first tab.</p>
                </TabPanel>
                <TabPanel value="tab2">
                    <h3>Content for Tab 2</h3>
                    <p>This is the content for the second tab.</p>
                </TabPanel>
                <TabPanel value="tab3">
                    <h3>Content for Tab 3</h3>
                    <p>This is the content for the third tab.</p>
                </TabPanel>
            </TabsContent>
        </Tabs>
    );
}
```

### Context-Based Composition
```jsx
// Context for form state
const FormContext = React.createContext();

function Form({ children, onSubmit, initialValues = {} }) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    
    const setValue = (name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
        
        // Clear error when user starts typing
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: null }));
        }
    };
    
    const setFieldTouched = (name) => {
        setTouched(prev => ({ ...prev, [name]: true }));
    };
    
    const setError = (name, error) => {
        setErrors(prev => ({ ...prev, [name]: error }));
    };
    
    const validate = () => {
        const newErrors = {};
        
        // Basic validation
        Object.entries(values).forEach(([name, value]) => {
            if (!value) {
                newErrors[name] = `${name} is required`;
            }
        });
        
        setErrors(newErrors);
        return Object.keys(newErrors).length === 0;
    };
    
    const handleSubmit = (e) => {
        e.preventDefault();
        
        if (validate()) {
            onSubmit(values);
        }
    };
    
    const contextValue = {
        values,
        errors,
        touched,
        setValue,
        setFieldTouched,
        setError,
        validate
    };
    
    return (
        <FormContext.Provider value={contextValue}>
            <form onSubmit={handleSubmit}>
                {children}
            </form>
        </FormContext.Provider>
    );
}

function FormField({ name, label, type = 'text', required = false }) {
    const { values, errors, touched, setValue, setFieldTouched } = useContext(FormContext);
    
    const handleChange = (e) => {
        setValue(name, e.target.value);
    };
    
    const handleBlur = () => {
        setFieldTouched(name);
    };
    
    return (
        <div className="form-field">
            <label htmlFor={name}>
                {label}
                {required && <span className="required">*</span>}
            </label>
            <input
                id={name}
                name={name}
                type={type}
                value={values[name] || ''}
                onChange={handleChange}
                onBlur={handleBlur}
                required={required}
            />
            {touched[name] && errors[name] && (
                <span className="error">{errors[name]}</span>
            )}
        </div>
    );
}

function FormSubmit({ children }) {
    const { validate } = useContext(FormContext);
    
    return (
        <button type="submit">
            {children}
        </button>
    );
}

// Usage
function ContactForm() {
    const handleSubmit = (values) => {
        console.log('Form submitted:', values);
    };
    
    return (
        <Form onSubmit={handleSubmit}>
            <FormField name="name" label="Name" required />
            <FormField name="email" label="Email" type="email" required />
            <FormField name="message" label="Message" required />
            <FormSubmit>Submit</FormSubmit>
        </Form>
    );
}
```

### Custom Hooks for Composition
```jsx
// Custom hook for form state
function useForm(initialValues = {}) {
    const [values, setValues] = useState(initialValues);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    
    const setValue = useCallback((name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
        
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: null }));
        }
    }, [errors]);
    
    const setFieldTouched = useCallback((name) => {
        setTouched(prev => ({ ...prev, [name]: true }));
    }, []);
    
    const setError = useCallback((name, error) => {
        setErrors(prev => ({ ...prev, [name]: error }));
    }, []);
    
    const validate = useCallback(() => {
        const newErrors = {};
        
        Object.entries(values).forEach(([name, value]) => {
            if (!value) {
                newErrors[name] = `${name} is required`;
            }
        });
        
        setErrors(newErrors);
        return Object.keys(newErrors).length === 0;
    }, [values]);
    
    const reset = useCallback(() => {
        setValues(initialValues);
        setErrors({});
        setTouched({});
    }, [initialValues]);
    
    return {
        values,
        errors,
        touched,
        setValue,
        setFieldTouched,
        setError,
        validate,
        reset
    };
}

// Custom hook for API calls
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

// Usage
function UserProfile() {
    const { values, errors, touched, setValue, setFieldTouched, validate, reset } = useForm({
        name: '',
        email: '',
        bio: ''
    });
    
    const { data: user, loading, error, refetch } = useApi('/api/user/profile');
    const [preferences, setPreferences] = useLocalStorage('userPreferences', {});
    
    const handleSubmit = (e) => {
        e.preventDefault();
        
        if (validate()) {
            console.log('Form submitted:', values);
        }
    };
    
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>Name</label>
                <input
                    value={values.name}
                    onChange={(e) => setValue('name', e.target.value)}
                    onBlur={() => setFieldTouched('name')}
                />
                {touched.name && errors.name && <span>{errors.name}</span>}
            </div>
            
            <div>
                <label>Email</label>
                <input
                    type="email"
                    value={values.email}
                    onChange={(e) => setValue('email', e.target.value)}
                    onBlur={() => setFieldTouched('email')}
                />
                {touched.email && errors.email && <span>{errors.email}</span>}
            </div>
            
            <div>
                <label>Bio</label>
                <textarea
                    value={values.bio}
                    onChange={(e) => setValue('bio', e.target.value)}
                    onBlur={() => setFieldTouched('bio')}
                />
                {touched.bio && errors.bio && <span>{errors.bio}</span>}
            </div>
            
            <button type="submit">Save</button>
            <button type="button" onClick={reset}>Reset</button>
        </form>
    );
}
```

## Exercise: Component Composition System

Create a comprehensive component composition system with:
- Container and presentational components
- Higher-order components
- Render props pattern
- Compound components
- Context-based composition
- Custom hooks for composition
- Reusable component library

## Key Takeaways
- Use composition over inheritance
- Separate concerns into focused components
- Leverage HOCs for cross-cutting concerns
- Use render props for flexible data sharing
- Create compound components for complex UI patterns
- Use context for deep component trees
- Extract logic into custom hooks
- Test components in isolation
- Document composition patterns
- Follow consistent naming conventions
