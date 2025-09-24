# React Fundamentals and Core Concepts

## Introduction
React is a library for building user interfaces using a component-based architecture. This lesson covers the fundamental concepts that form the foundation of React development.

## Component-Based Architecture

### Functional Components vs Class Components
```jsx
// Functional Component (Modern Approach)
function Welcome({ name, age }) {
    return (
        <div>
            <h1>Welcome, {name}!</h1>
            <p>You are {age} years old.</p>
        </div>
    );
}

// Arrow Function Component
const Welcome = ({ name, age }) => (
    <div>
        <h1>Welcome, {name}!</h1>
        <p>You are {age} years old.</p>
    </div>
);

// Class Component (Legacy - still useful for understanding)
class WelcomeClass extends React.Component {
    render() {
        const { name, age } = this.props;
        return (
            <div>
                <h1>Welcome, {name}!</h1>
                <p>You are {age} years old.</p>
            </div>
        );
    }
}

// Usage
function App() {
    return (
        <div>
            <Welcome name="John" age={30} />
            <Welcome name="Jane" age={25} />
        </div>
    );
}
```

**Code Explanation:**
- `function Welcome({ name, age })`: Functional component with destructured props
- `{ name, age }`: Destructuring assignment extracts props directly in function parameters
- `return (...)`: JSX return statement (parentheses optional for single expression)
- `{name}`: JSX expression interpolation for dynamic content
- `const Welcome = ({ name, age }) => (...)`: Arrow function component (implicit return)
- `class WelcomeClass extends React.Component`: Class component extending React base class
- `render()`: Required method in class components that returns JSX
- `this.props`: Access to props in class components
- `<Welcome name="John" age={30} />`: Component usage with props passed as attributes

**Benefits:**
- Functional components are simpler and more readable
- Arrow functions provide concise syntax
- Class components help understand React's evolution
- Props enable component reusability and customization

### Component Composition Patterns
```jsx
// Children Prop Usage
function Card({ children, title }) {
    return (
        <div className="card">
            {title && <h3 className="card-title">{title}</h3>}
            <div className="card-content">
                {children}
            </div>
        </div>
    );
}

// Using Card with children
function UserProfile({ user }) {
    return (
        <Card title="User Profile">
            <img src={user.avatar} alt={user.name} />
            <h4>{user.name}</h4>
            <p>{user.email}</p>
        </Card>
    );
}

// Render Props Pattern
function DataFetcher({ url, render }) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        fetch(url)
            .then(response => response.json())
            .then(data => {
                setData(data);
                setLoading(false);
            })
            .catch(error => {
                setError(error);
                setLoading(false);
            });
    }, [url]);

    return render({ data, loading, error });
}

// Using render props
function UserList() {
    return (
        <DataFetcher 
            url="/api/users"
            render={({ data, loading, error }) => {
                if (loading) return <div>Loading...</div>;
                if (error) return <div>Error: {error.message}</div>;
                return (
                    <ul>
                        {data.map(user => (
                            <li key={user.id}>{user.name}</li>
                        ))}
                    </ul>
                );
            }}
        />
    );
}
```

## JSX Syntax Deep Understanding

### JSX Expressions and Conditional Rendering
```jsx
function ConditionalRendering({ user, isLoggedIn }) {
    return (
        <div>
            {/* Conditional rendering with && */}
            {isLoggedIn && <h1>Welcome back, {user.name}!</h1>}
            
            {/* Ternary operator for conditional rendering */}
            {isLoggedIn ? (
                <button onClick={() => logout()}>Logout</button>
            ) : (
                <button onClick={() => login()}>Login</button>
            )}
            
            {/* Complex conditional logic */}
            {(() => {
                if (user.role === 'admin') {
                    return <AdminPanel />;
                } else if (user.role === 'user') {
                    return <UserDashboard />;
                } else {
                    return <GuestView />;
                }
            })()}
            
            {/* Array rendering with keys */}
            <ul>
                {user.permissions.map(permission => (
                    <li key={permission.id}>
                        {permission.name}
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

### Fragment Shorthand and Key Props
```jsx
// Fragment shorthand for grouping elements
function FragmentExample() {
    return (
        <>
            <h1>Title</h1>
            <p>Paragraph 1</p>
            <p>Paragraph 2</p>
        </>
    );
}

// Explicit Fragment with key
function ListWithKeys({ items }) {
    return (
        <React.Fragment>
            {items.map(item => (
                <React.Fragment key={item.id}>
                    <dt>{item.title}</dt>
                    <dd>{item.description}</dd>
                </React.Fragment>
            ))}
        </React.Fragment>
    );
}

// Avoiding unnecessary wrapper divs
function Layout({ children }) {
    return (
        <div className="layout">
            <header>Header</header>
            <main>{children}</main>
            <footer>Footer</footer>
        </div>
    );
}
```

## Props System Mastery

### Prop Validation and Default Values
```jsx
import PropTypes from 'prop-types';

// Component with PropTypes validation
function UserCard({ 
    name, 
    age, 
    email, 
    avatar, 
    isActive = true,
    onEdit,
    permissions = []
}) {
    return (
        <div className={`user-card ${isActive ? 'active' : 'inactive'}`}>
            <img src={avatar} alt={name} />
            <h3>{name}</h3>
            <p>Age: {age}</p>
            <p>Email: {email}</p>
            {permissions.length > 0 && (
                <div>
                    <h4>Permissions:</h4>
                    <ul>
                        {permissions.map(permission => (
                            <li key={permission}>{permission}</li>
                        ))}
                    </ul>
                </div>
            )}
            <button onClick={onEdit}>Edit User</button>
        </div>
    );
}

// PropTypes definition
UserCard.propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number.isRequired,
    email: PropTypes.string.isRequired,
    avatar: PropTypes.string,
    isActive: PropTypes.bool,
    onEdit: PropTypes.func.isRequired,
    permissions: PropTypes.arrayOf(PropTypes.string)
};

// Default props
UserCard.defaultProps = {
    avatar: '/default-avatar.png',
    isActive: true,
    permissions: []
};
```

### Advanced Props Patterns
```jsx
// Rest and spread with props
function FlexibleButton({ 
    variant = 'primary', 
    size = 'medium', 
    children, 
    ...rest 
}) {
    const className = `btn btn-${variant} btn-${size}`;
    
    return (
        <button className={className} {...rest}>
            {children}
        </button>
    );
}

// Using FlexibleButton
function ButtonExamples() {
    return (
        <div>
            <FlexibleButton onClick={() => console.log('Clicked')}>
                Primary Button
            </FlexibleButton>
            
            <FlexibleButton 
                variant="secondary" 
                size="large"
                disabled
            >
                Large Secondary Button
            </FlexibleButton>
            
            <FlexibleButton 
                variant="danger"
                onClick={() => confirm('Delete?')}
                style={{ marginLeft: '10px' }}
            >
                Delete
            </FlexibleButton>
        </div>
    );
}

// Higher-Order Component for prop injection
function withLoading(WrappedComponent) {
    return function WithLoadingComponent({ isLoading, ...props }) {
        if (isLoading) {
            return <div>Loading...</div>;
        }
        return <WrappedComponent {...props} />;
    };
}

// Using HOC
const UserListWithLoading = withLoading(UserList);

function App() {
    const [loading, setLoading] = useState(true);
    const [users, setUsers] = useState([]);

    useEffect(() => {
        fetchUsers().then(users => {
            setUsers(users);
            setLoading(false);
        });
    }, []);

    return (
        <UserListWithLoading 
            isLoading={loading}
            users={users}
        />
    );
}
```

## Component Reusability Principles

### Compound Components Pattern
```jsx
// Compound Components for flexible APIs
const Tabs = ({ children, defaultTab }) => {
    const [activeTab, setActiveTab] = useState(defaultTab);

    return (
        <div className="tabs">
            {React.Children.map(children, child => {
                if (child.type === TabList) {
                    return React.cloneElement(child, { 
                        activeTab, 
                        setActiveTab 
                    });
                }
                if (child.type === TabPanels) {
                    return React.cloneElement(child, { activeTab });
                }
                return child;
            })}
        </div>
    );
};

const TabList = ({ activeTab, setActiveTab, children }) => (
    <div className="tab-list">
        {React.Children.map(children, (child, index) => {
            const tabId = child.props.tabId || index;
            return React.cloneElement(child, {
                isActive: activeTab === tabId,
                onClick: () => setActiveTab(tabId)
            });
        })}
    </div>
);

const Tab = ({ isActive, onClick, children }) => (
    <button 
        className={`tab ${isActive ? 'active' : ''}`}
        onClick={onClick}
    >
        {children}
    </button>
);

const TabPanels = ({ activeTab, children }) => (
    <div className="tab-panels">
        {React.Children.map(children, (child, index) => {
            const panelId = child.props.tabId || index;
            return React.cloneElement(child, {
                isActive: activeTab === panelId
            });
        })}
    </div>
);

const TabPanel = ({ isActive, children }) => (
    <div className={`tab-panel ${isActive ? 'active' : ''}`}>
        {children}
    </div>
);

// Using compound components
function TabExample() {
    return (
        <Tabs defaultTab={0}>
            <TabList>
                <Tab tabId={0}>Profile</Tab>
                <Tab tabId={1}>Settings</Tab>
                <Tab tabId={2}>Messages</Tab>
            </TabList>
            
            <TabPanels>
                <TabPanel tabId={0}>
                    <h3>Profile Information</h3>
                    <p>User profile details...</p>
                </TabPanel>
                <TabPanel tabId={1}>
                    <h3>Account Settings</h3>
                    <p>Settings configuration...</p>
                </TabPanel>
                <TabPanel tabId={2}>
                    <h3>Messages</h3>
                    <p>Message history...</p>
                </TabPanel>
            </TabPanels>
        </Tabs>
    );
}
```

### Render Props and Children as Function
```jsx
// Toggle component with render props
function Toggle({ children }) {
    const [isOn, setIsOn] = useState(false);
    
    const toggle = () => setIsOn(!isOn);
    
    return children({ isOn, toggle });
}

// Using Toggle with render props
function ToggleExample() {
    return (
        <Toggle>
            {({ isOn, toggle }) => (
                <div>
                    <button onClick={toggle}>
                        {isOn ? 'ON' : 'OFF'}
                    </button>
                    <p>The switch is {isOn ? 'ON' : 'OFF'}</p>
                </div>
            )}
        </Toggle>
    );
}

// Form component with render props
function Form({ onSubmit, children }) {
    const [values, setValues] = useState({});
    const [errors, setErrors] = useState({});
    
    const handleChange = (name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
        // Clear error when user starts typing
        if (errors[name]) {
            setErrors(prev => ({ ...prev, [name]: null }));
        }
    };
    
    const handleSubmit = (e) => {
        e.preventDefault();
        onSubmit(values);
    };
    
    return children({
        values,
        errors,
        handleChange,
        handleSubmit
    });
}

// Using Form with render props
function ContactForm() {
    return (
        <Form onSubmit={(values) => console.log('Form submitted:', values)}>
            {({ values, errors, handleChange, handleSubmit }) => (
                <form onSubmit={handleSubmit}>
                    <div>
                        <input
                            type="text"
                            name="name"
                            value={values.name || ''}
                            onChange={(e) => handleChange('name', e.target.value)}
                            placeholder="Name"
                        />
                        {errors.name && <span className="error">{errors.name}</span>}
                    </div>
                    
                    <div>
                        <input
                            type="email"
                            name="email"
                            value={values.email || ''}
                            onChange={(e) => handleChange('email', e.target.value)}
                            placeholder="Email"
                        />
                        {errors.email && <span className="error">{errors.email}</span>}
                    </div>
                    
                    <button type="submit">Submit</button>
                </form>
            )}
        </Form>
    );
}
```

## Exercise: Component Library Foundation

Create a reusable component library with:
- Button component with variants and sizes
- Card component with flexible content
- Modal component with render props
- Form components with validation
- Layout components with composition patterns

## Key Takeaways
- Functional components are the modern approach in React
- Use composition over inheritance for flexible components
- Props provide a clean interface for component communication
- Render props enable powerful component patterns
- Compound components create flexible APIs
- Always use keys when rendering lists
- PropTypes help catch bugs during development
- Default props provide sensible fallbacks
