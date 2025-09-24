# Component Design Principles

## Introduction
Component design principles are fundamental to creating maintainable, reusable, and scalable React applications. This lesson covers SOLID principles, composition patterns, and best practices for component architecture.

## SOLID Principles in React

### Single Responsibility Principle (SRP)
```jsx
// Bad: Component with multiple responsibilities
function UserProfile({ user }) {
    const [isEditing, setIsEditing] = useState(false);
    const [userData, setUserData] = useState(user);
    const [isLoading, setIsLoading] = useState(false);
    
    const handleEdit = () => {
        setIsEditing(true);
    };
    
    const handleSave = async () => {
        setIsLoading(true);
        try {
            await updateUser(userData);
            setIsEditing(false);
        } catch (error) {
            console.error('Failed to update user:', error);
        } finally {
            setIsLoading(false);
        }
    };
    
    const handleCancel = () => {
        setUserData(user);
        setIsEditing(false);
    };
    
    return (
        <div className="user-profile">
            <div className="profile-header">
                <h1>{userData.name}</h1>
                <button onClick={handleEdit}>Edit</button>
            </div>
            
            <div className="profile-content">
                {isEditing ? (
                    <UserEditForm
                        user={userData}
                        onSave={handleSave}
                        onCancel={handleCancel}
                        isLoading={isLoading}
                    />
                ) : (
                    <UserDisplay user={userData} />
                )}
            </div>
            
            <div className="profile-actions">
                <button onClick={() => deleteUser(user.id)}>Delete</button>
            </div>
        </div>
    );
}

// Good: Separated responsibilities
function UserProfile({ user }) {
    return (
        <div className="user-profile">
            <UserProfileHeader user={user} />
            <UserProfileContent user={user} />
            <UserProfileActions user={user} />
        </div>
    );
}

function UserProfileHeader({ user }) {
    return (
        <div className="profile-header">
            <h1>{user.name}</h1>
            <UserEditButton user={user} />
        </div>
    );
}

function UserProfileContent({ user }) {
    const [isEditing, setIsEditing] = useState(false);
    
    return (
        <div className="profile-content">
            {isEditing ? (
                <UserEditForm user={user} onCancel={() => setIsEditing(false)} />
            ) : (
                <UserDisplay user={user} />
            )}
        </div>
    );
}

function UserProfileActions({ user }) {
    return (
        <div className="profile-actions">
            <UserDeleteButton user={user} />
        </div>
    );
}

function UserEditButton({ user }) {
    const [isEditing, setIsEditing] = useState(false);
    
    return (
        <button onClick={() => setIsEditing(true)}>
            Edit
        </button>
    );
}

function UserEditForm({ user, onCancel }) {
    const [userData, setUserData] = useState(user);
    const [isLoading, setIsLoading] = useState(false);
    
    const handleSave = async () => {
        setIsLoading(true);
        try {
            await updateUser(userData);
            onCancel();
        } catch (error) {
            console.error('Failed to update user:', error);
        } finally {
            setIsLoading(false);
        }
    };
    
    return (
        <form onSubmit={handleSave}>
            <input
                value={userData.name}
                onChange={(e) => setUserData({ ...userData, name: e.target.value })}
            />
            <button type="submit" disabled={isLoading}>
                {isLoading ? 'Saving...' : 'Save'}
            </button>
            <button type="button" onClick={onCancel}>
                Cancel
            </button>
        </form>
    );
}

function UserDisplay({ user }) {
    return (
        <div className="user-display">
            <p>Name: {user.name}</p>
            <p>Email: {user.email}</p>
            <p>Role: {user.role}</p>
        </div>
    );
}

function UserDeleteButton({ user }) {
    const [isDeleting, setIsDeleting] = useState(false);
    
    const handleDelete = async () => {
        setIsDeleting(true);
        try {
            await deleteUser(user.id);
        } catch (error) {
            console.error('Failed to delete user:', error);
        } finally {
            setIsDeleting(false);
        }
    };
    
    return (
        <button onClick={handleDelete} disabled={isDeleting}>
            {isDeleting ? 'Deleting...' : 'Delete'}
        </button>
    );
}
```

### Open/Closed Principle (OCP)
```jsx
// Bad: Component that needs modification for new features
function Button({ type, onClick, children }) {
    const getButtonClass = () => {
        switch (type) {
            case 'primary':
                return 'btn btn-primary';
            case 'secondary':
                return 'btn btn-secondary';
            case 'danger':
                return 'btn btn-danger';
            case 'success':
                return 'btn btn-success';
            default:
                return 'btn';
        }
    };
    
    return (
        <button className={getButtonClass()} onClick={onClick}>
            {children}
        </button>
    );
}

// Good: Open for extension, closed for modification
function Button({ variant, size, onClick, children, ...props }) {
    const baseClass = 'btn';
    const variantClass = `btn-${variant}`;
    const sizeClass = `btn-${size}`;
    
    return (
        <button
            className={`${baseClass} ${variantClass} ${sizeClass}`}
            onClick={onClick}
            {...props}
        >
            {children}
        </button>
    );
}

// Extensible button variants
function PrimaryButton(props) {
    return <Button variant="primary" {...props} />;
}

function SecondaryButton(props) {
    return <Button variant="secondary" {...props} />;
}

function DangerButton(props) {
    return <Button variant="danger" {...props} />;
}

function SuccessButton(props) {
    return <Button variant="success" {...props} />;
}

// Custom button with additional functionality
function IconButton({ icon, ...props }) {
    return (
        <Button {...props}>
            <span className="icon">{icon}</span>
            {props.children}
        </Button>
    );
}

// Usage
function App() {
    return (
        <div>
            <PrimaryButton onClick={() => console.log('Primary clicked')}>
                Primary
            </PrimaryButton>
            <SecondaryButton onClick={() => console.log('Secondary clicked')}>
                Secondary
            </SecondaryButton>
            <DangerButton onClick={() => console.log('Danger clicked')}>
                Danger
            </DangerButton>
            <IconButton icon="★" onClick={() => console.log('Icon clicked')}>
                Star
            </IconButton>
        </div>
    );
}
```

### Liskov Substitution Principle (LSP)
```jsx
// Base component interface
function BaseInput({ value, onChange, ...props }) {
    return (
        <input
            value={value}
            onChange={onChange}
            {...props}
        />
    );
}

// Substitutable components
function TextInput({ value, onChange, ...props }) {
    return (
        <BaseInput
            type="text"
            value={value}
            onChange={onChange}
            {...props}
        />
    );
}

function EmailInput({ value, onChange, ...props }) {
    return (
        <BaseInput
            type="email"
            value={value}
            onChange={onChange}
            {...props}
        />
    );
}

function PasswordInput({ value, onChange, ...props }) {
    return (
        <BaseInput
            type="password"
            value={value}
            onChange={onChange}
            {...props}
        />
    );
}

// Custom input with additional functionality
function NumberInput({ value, onChange, min, max, ...props }) {
    const handleChange = (e) => {
        const newValue = e.target.value;
        if (newValue === '' || (min <= newValue && newValue <= max)) {
            onChange(e);
        }
    };
    
    return (
        <BaseInput
            type="number"
            value={value}
            onChange={handleChange}
            min={min}
            max={max}
            {...props}
        />
    );
}

// Form component that works with any input type
function Form({ inputs, onSubmit }) {
    const [values, setValues] = useState({});
    
    const handleInputChange = (name, value) => {
        setValues(prev => ({ ...prev, [name]: value }));
    };
    
    const handleSubmit = (e) => {
        e.preventDefault();
        onSubmit(values);
    };
    
    return (
        <form onSubmit={handleSubmit}>
            {inputs.map(input => (
                <div key={input.name}>
                    <label>{input.label}</label>
                    {React.createElement(input.component, {
                        value: values[input.name] || '',
                        onChange: (e) => handleInputChange(input.name, e.target.value),
                        ...input.props
                    })}
                </div>
            ))}
            <button type="submit">Submit</button>
        </form>
    );
}

// Usage
function App() {
    const inputs = [
        { name: 'name', label: 'Name', component: TextInput },
        { name: 'email', label: 'Email', component: EmailInput },
        { name: 'password', label: 'Password', component: PasswordInput },
        { name: 'age', label: 'Age', component: NumberInput, props: { min: 0, max: 120 } }
    ];
    
    const handleSubmit = (values) => {
        console.log('Form submitted:', values);
    };
    
    return <Form inputs={inputs} onSubmit={handleSubmit} />;
}
```

### Interface Segregation Principle (ISP)
```jsx
// Bad: Component with too many responsibilities
function UserComponent({ user, onEdit, onDelete, onUpdate, onSave, onCancel }) {
    return (
        <div>
            <h1>{user.name}</h1>
            <button onClick={onEdit}>Edit</button>
            <button onClick={onDelete}>Delete</button>
            <button onClick={onUpdate}>Update</button>
            <button onClick={onSave}>Save</button>
            <button onClick={onCancel}>Cancel</button>
        </div>
    );
}

// Good: Segregated interfaces
function UserDisplay({ user }) {
    return (
        <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
        </div>
    );
}

function UserActions({ user, onEdit, onDelete }) {
    return (
        <div>
            <button onClick={() => onEdit(user)}>Edit</button>
            <button onClick={() => onDelete(user.id)}>Delete</button>
        </div>
    );
}

function UserEditForm({ user, onSave, onCancel }) {
    const [userData, setUserData] = useState(user);
    
    return (
        <form onSubmit={(e) => { e.preventDefault(); onSave(userData); }}>
            <input
                value={userData.name}
                onChange={(e) => setUserData({ ...userData, name: e.target.value })}
            />
            <button type="submit">Save</button>
            <button type="button" onClick={onCancel}>Cancel</button>
        </form>
    );
}

// Composed component
function UserComponent({ user, onEdit, onDelete, onSave, onCancel }) {
    return (
        <div>
            <UserDisplay user={user} />
            <UserActions user={user} onEdit={onEdit} onDelete={onDelete} />
            <UserEditForm user={user} onSave={onSave} onCancel={onCancel} />
        </div>
    );
}
```

### Dependency Inversion Principle (DIP)
```jsx
// Bad: Component depends on concrete implementation
function UserList() {
    const [users, setUsers] = useState([]);
    
    useEffect(() => {
        // Direct dependency on fetch API
        fetch('/api/users')
            .then(response => response.json())
            .then(data => setUsers(data));
    }, []);
    
    return (
        <div>
            {users.map(user => (
                <div key={user.id}>{user.name}</div>
            ))}
        </div>
    );
}

// Good: Component depends on abstraction
function UserList({ userService }) {
    const [users, setUsers] = useState([]);
    
    useEffect(() => {
        userService.getUsers()
            .then(data => setUsers(data));
    }, [userService]);
    
    return (
        <div>
            {users.map(user => (
                <div key={user.id}>{user.name}</div>
            ))}
        </div>
    );
}

// Abstract service interface
class UserService {
    async getUsers() {
        throw new Error('getUsers must be implemented');
    }
    
    async getUser(id) {
        throw new Error('getUser must be implemented');
    }
    
    async createUser(user) {
        throw new Error('createUser must be implemented');
    }
    
    async updateUser(id, user) {
        throw new Error('updateUser must be implemented');
    }
    
    async deleteUser(id) {
        throw new Error('deleteUser must be implemented');
    }
}

// Concrete implementation
class ApiUserService extends UserService {
    constructor(baseUrl) {
        super();
        this.baseUrl = baseUrl;
    }
    
    async getUsers() {
        const response = await fetch(`${this.baseUrl}/users`);
        return response.json();
    }
    
    async getUser(id) {
        const response = await fetch(`${this.baseUrl}/users/${id}`);
        return response.json();
    }
    
    async createUser(user) {
        const response = await fetch(`${this.baseUrl}/users`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(user)
        });
        return response.json();
    }
    
    async updateUser(id, user) {
        const response = await fetch(`${this.baseUrl}/users/${id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(user)
        });
        return response.json();
    }
    
    async deleteUser(id) {
        const response = await fetch(`${this.baseUrl}/users/${id}`, {
            method: 'DELETE'
        });
        return response.json();
    }
}

// Mock implementation for testing
class MockUserService extends UserService {
    constructor() {
        super();
        this.users = [
            { id: 1, name: 'John Doe', email: 'john@example.com' },
            { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
        ];
    }
    
    async getUsers() {
        return this.users;
    }
    
    async getUser(id) {
        return this.users.find(user => user.id === id);
    }
    
    async createUser(user) {
        const newUser = { ...user, id: this.users.length + 1 };
        this.users.push(newUser);
        return newUser;
    }
    
    async updateUser(id, user) {
        const index = this.users.findIndex(u => u.id === id);
        if (index !== -1) {
            this.users[index] = { ...user, id };
            return this.users[index];
        }
        throw new Error('User not found');
    }
    
    async deleteUser(id) {
        const index = this.users.findIndex(u => u.id === id);
        if (index !== -1) {
            this.users.splice(index, 1);
            return true;
        }
        throw new Error('User not found');
    }
}

// Usage
function App() {
    const userService = new ApiUserService('/api');
    // const userService = new MockUserService(); // For testing
    
    return <UserList userService={userService} />;
}
```

## Exercise: Component Design System

Create a comprehensive component design system with:
- SOLID principles implementation
- Component composition patterns
- Interface segregation
- Dependency inversion
- Reusable component library
- Design system documentation

## Key Takeaways
- Apply SOLID principles to React components
- Use composition over inheritance
- Separate concerns into focused components
- Design components to be open for extension
- Use abstractions to reduce coupling
- Create interfaces that are specific to client needs
- Test components in isolation
- Document component APIs and usage patterns
- Use TypeScript for better type safety
- Follow consistent naming conventions
