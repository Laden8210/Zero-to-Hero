# JavaScript Modules and Patterns

## Introduction
JavaScript modules and design patterns are essential for creating maintainable, scalable applications. This lesson covers ES6 modules, design patterns, and code organization strategies.

## ES6 Module System

### Export Types
```javascript
// Named exports
export const API_BASE_URL = 'https://api.example.com';
export const MAX_RETRIES = 3;

export function fetchUser(userId) {
    return fetch(`${API_BASE_URL}/users/${userId}`)
        .then(response => response.json());
}

export class UserService {
    constructor(apiUrl) {
        this.apiUrl = apiUrl;
    }
    
    async getUser(id) {
        const response = await fetch(`${this.apiUrl}/users/${id}`);
        return response.json();
    }
}

// Default export
export default class ApiClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseUrl}${endpoint}`;
        const response = await fetch(url, options);
        return response.json();
    }
}

// Mixed exports
export const config = {
    apiUrl: 'https://api.example.com',
    timeout: 5000
};

export default function createApiClient(config) {
    return new ApiClient(config.apiUrl);
}
```

### Import Syntax
```javascript
// Named imports
import { API_BASE_URL, fetchUser, UserService } from './api.js';

// Default import
import ApiClient from './api.js';

// Mixed imports
import ApiClient, { config, fetchUser } from './api.js';

// Namespace import
import * as api from './api.js';

// Renamed imports
import { fetchUser as getUser } from './api.js';
import { default as Client } from './api.js';

// Side-effect imports
import './styles.css';
import './analytics.js';

// Dynamic imports
async function loadModule() {
    const module = await import('./dynamic-module.js');
    return module.default;
}

// Conditional imports
if (condition) {
    const { feature } = await import('./feature.js');
    feature.init();
}
```

### Module Organization
```javascript
// utils/index.js - Barrel exports
export { formatDate, formatCurrency } from './date.js';
export { validateEmail, validatePhone } from './validation.js';
export { debounce, throttle } from './performance.js';

// services/userService.js
import { ApiClient } from '../api/client.js';
import { validateEmail } from '../utils/validation.js';

export class UserService {
    constructor(apiClient) {
        this.apiClient = apiClient;
    }
    
    async createUser(userData) {
        if (!validateEmail(userData.email)) {
            throw new Error('Invalid email address');
        }
        
        return this.apiClient.post('/users', userData);
    }
    
    async updateUser(userId, updates) {
        return this.apiClient.patch(`/users/${userId}`, updates);
    }
    
    async deleteUser(userId) {
        return this.apiClient.delete(`/users/${userId}`);
    }
}

// components/UserCard.js
import { UserService } from '../services/userService.js';
import { formatDate } from '../utils/index.js';

export class UserCard {
    constructor(userService) {
        this.userService = userService;
    }
    
    render(user) {
        return `
            <div class="user-card">
                <h3>${user.name}</h3>
                <p>Email: ${user.email}</p>
                <p>Created: ${formatDate(user.createdAt)}</p>
            </div>
        `;
    }
}
```

## Design Patterns Implementation

### Module Pattern
```javascript
// Module pattern for encapsulation
const UserModule = (function() {
    // Private variables
    let users = [];
    let currentUser = null;
    
    // Private methods
    function validateUser(user) {
        return user && user.email && user.name;
    }
    
    function findUserById(id) {
        return users.find(user => user.id === id);
    }
    
    // Public API
    return {
        // Public methods
        addUser(user) {
            if (!validateUser(user)) {
                throw new Error('Invalid user data');
            }
            
            const newUser = {
                id: Date.now(),
                ...user,
                createdAt: new Date()
            };
            
            users.push(newUser);
            return newUser;
        },
        
        getUser(id) {
            return findUserById(id);
        },
        
        getAllUsers() {
            return [...users]; // Return copy to prevent mutation
        },
        
        setCurrentUser(user) {
            if (findUserById(user.id)) {
                currentUser = user;
                return true;
            }
            return false;
        },
        
        getCurrentUser() {
            return currentUser;
        },
        
        removeUser(id) {
            const index = users.findIndex(user => user.id === id);
            if (index !== -1) {
                users.splice(index, 1);
                return true;
            }
            return false;
        }
    };
})();

// Usage
const user = UserModule.addUser({
    name: 'John Doe',
    email: 'john@example.com'
});

console.log(UserModule.getAllUsers());
```

### Factory Pattern
```javascript
// Factory pattern for object creation
class WidgetFactory {
    static createWidget(type, config) {
        switch (type) {
            case 'button':
                return new ButtonWidget(config);
            case 'input':
                return new InputWidget(config);
            case 'card':
                return new CardWidget(config);
            default:
                throw new Error(`Unknown widget type: ${type}`);
        }
    }
}

class ButtonWidget {
    constructor(config) {
        this.text = config.text || 'Button';
        this.variant = config.variant || 'primary';
        this.onClick = config.onClick || (() => {});
    }
    
    render() {
        return `
            <button class="btn btn-${this.variant}" onclick="${this.onClick}">
                ${this.text}
            </button>
        `;
    }
}

class InputWidget {
    constructor(config) {
        this.type = config.type || 'text';
        this.placeholder = config.placeholder || '';
        this.value = config.value || '';
    }
    
    render() {
        return `
            <input 
                type="${this.type}" 
                placeholder="${this.placeholder}"
                value="${this.value}"
            >
        `;
    }
}

class CardWidget {
    constructor(config) {
        this.title = config.title || '';
        this.content = config.content || '';
        this.image = config.image || '';
    }
    
    render() {
        return `
            <div class="card">
                ${this.image ? `<img src="${this.image}" alt="${this.title}">` : ''}
                <div class="card-body">
                    <h3>${this.title}</h3>
                    <p>${this.content}</p>
                </div>
            </div>
        `;
    }
}

// Usage
const button = WidgetFactory.createWidget('button', {
    text: 'Click me',
    variant: 'secondary',
    onClick: 'handleClick()'
});

const input = WidgetFactory.createWidget('input', {
    type: 'email',
    placeholder: 'Enter your email'
});

const card = WidgetFactory.createWidget('card', {
    title: 'Card Title',
    content: 'Card content goes here',
    image: 'image.jpg'
});
```

### Observer Pattern
```javascript
// Observer pattern for event-driven architecture
class EventEmitter {
    constructor() {
        this.events = {};
    }
    
    on(event, callback) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(callback);
    }
    
    off(event, callback) {
        if (!this.events[event]) return;
        
        this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
    
    emit(event, data) {
        if (!this.events[event]) return;
        
        this.events[event].forEach(callback => {
            try {
                callback(data);
            } catch (error) {
                console.error(`Error in event handler for ${event}:`, error);
            }
        });
    }
    
    once(event, callback) {
        const onceCallback = (data) => {
            callback(data);
            this.off(event, onceCallback);
        };
        this.on(event, onceCallback);
    }
}

// Usage example
class UserManager extends EventEmitter {
    constructor() {
        super();
        this.users = [];
    }
    
    addUser(user) {
        this.users.push(user);
        this.emit('userAdded', user);
        this.emit('usersChanged', this.users);
    }
    
    removeUser(userId) {
        const index = this.users.findIndex(user => user.id === userId);
        if (index !== -1) {
            const user = this.users.splice(index, 1)[0];
            this.emit('userRemoved', user);
            this.emit('usersChanged', this.users);
        }
    }
}

// Usage
const userManager = new UserManager();

userManager.on('userAdded', (user) => {
    console.log('New user added:', user);
    updateUserList();
});

userManager.on('userRemoved', (user) => {
    console.log('User removed:', user);
    updateUserList();
});

userManager.on('usersChanged', (users) => {
    console.log('Total users:', users.length);
});
```

### Singleton Pattern
```javascript
// Singleton pattern for shared resources
class DatabaseConnection {
    constructor() {
        if (DatabaseConnection.instance) {
            return DatabaseConnection.instance;
        }
        
        this.connection = null;
        this.isConnected = false;
        DatabaseConnection.instance = this;
    }
    
    async connect(config) {
        if (this.isConnected) {
            return this.connection;
        }
        
        try {
            // Simulate database connection
            this.connection = await this.establishConnection(config);
            this.isConnected = true;
            return this.connection;
        } catch (error) {
            throw new Error(`Failed to connect to database: ${error.message}`);
        }
    }
    
    async establishConnection(config) {
        // Simulate connection establishment
        return new Promise((resolve) => {
            setTimeout(() => {
                resolve({
                    host: config.host,
                    port: config.port,
                    database: config.database
                });
            }, 1000);
        });
    }
    
    disconnect() {
        this.connection = null;
        this.isConnected = false;
    }
    
    query(sql) {
        if (!this.isConnected) {
            throw new Error('Not connected to database');
        }
        
        console.log('Executing query:', sql);
        // Simulate query execution
        return Promise.resolve([]);
    }
}

// Usage
const db1 = new DatabaseConnection();
const db2 = new DatabaseConnection();

console.log(db1 === db2); // true - same instance

await db1.connect({
    host: 'localhost',
    port: 5432,
    database: 'myapp'
});

// db2 is already connected because it's the same instance
console.log(db2.isConnected); // true
```

## Functional Programming Concepts

### Pure Functions and Immutability
```javascript
// Pure functions
function add(a, b) {
    return a + b;
}

function multiply(a, b) {
    return a * b;
}

function calculateTotal(items) {
    return items.reduce((total, item) => total + item.price, 0);
}

// Immutable data operations
class ImmutableDataManager {
    static addItem(list, item) {
        return [...list, item];
    }
    
    static removeItem(list, id) {
        return list.filter(item => item.id !== id);
    }
    
    static updateItem(list, id, updates) {
        return list.map(item => 
            item.id === id 
                ? { ...item, ...updates }
                : item
        );
    }
    
    static sortItems(list, key) {
        return [...list].sort((a, b) => {
            if (a[key] < b[key]) return -1;
            if (a[key] > b[key]) return 1;
            return 0;
        });
    }
}

// Usage
let items = [
    { id: 1, name: 'Apple', price: 1.50 },
    { id: 2, name: 'Banana', price: 0.75 }
];

// Add item (immutable)
items = ImmutableDataManager.addItem(items, { id: 3, name: 'Orange', price: 2.00 });

// Update item (immutable)
items = ImmutableDataManager.updateItem(items, 1, { price: 1.75 });

// Sort items (immutable)
items = ImmutableDataManager.sortItems(items, 'name');
```

### Higher-Order Functions
```javascript
// Higher-order functions
function createValidator(rules) {
    return function(data) {
        const errors = [];
        
        Object.entries(rules).forEach(([field, rule]) => {
            const value = data[field];
            const error = rule(value);
            if (error) {
                errors.push({ field, error });
            }
        });
        
        return errors;
    };
}

function createLogger(level) {
    return function(message, data = null) {
        const timestamp = new Date().toISOString();
        const logEntry = {
            timestamp,
            level,
            message,
            data
        };
        
        console.log(JSON.stringify(logEntry));
    };
}

function createRetry(fn, maxAttempts = 3) {
    return async function(...args) {
        let lastError;
        
        for (let attempt = 1; attempt <= maxAttempts; attempt++) {
            try {
                return await fn(...args);
            } catch (error) {
                lastError = error;
                console.log(`Attempt ${attempt} failed:`, error.message);
                
                if (attempt === maxAttempts) {
                    throw lastError;
                }
                
                // Wait before retry
                await new Promise(resolve => setTimeout(resolve, 1000 * attempt));
            }
        }
    };
}

// Usage
const validateUser = createValidator({
    name: (value) => !value ? 'Name is required' : null,
    email: (value) => !value || !value.includes('@') ? 'Valid email is required' : null,
    age: (value) => !value || value < 18 ? 'Age must be 18 or older' : null
});

const logError = createLogger('ERROR');
const logInfo = createLogger('INFO');

const fetchWithRetry = createRetry(fetch, 3);

// Array methods (higher-order functions)
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
const hasEven = numbers.some(n => n % 2 === 0);
const allPositive = numbers.every(n => n > 0);

// Functional composition
function compose(...fns) {
    return function(value) {
        return fns.reduceRight((acc, fn) => fn(acc), value);
    };
}

function pipe(...fns) {
    return function(value) {
        return fns.reduce((acc, fn) => fn(acc), value);
    };
}

// Usage
const addOne = x => x + 1;
const multiplyByTwo = x => x * 2;
const square = x => x * x;

const transform = pipe(addOne, multiplyByTwo, square);
const result = transform(3); // ((3 + 1) * 2)² = 64
```

## Exercise: Modular JavaScript Application

Create a modular JavaScript application with:
- ES6 modules with proper imports/exports
- Design patterns (Module, Factory, Observer, Singleton)
- Functional programming concepts
- Pure functions and immutable data
- Higher-order functions and composition
- Event-driven architecture

## Key Takeaways
- Use ES6 modules for better code organization
- Implement design patterns for common problems
- Apply functional programming concepts
- Create pure functions for predictable behavior
- Use immutable data operations
- Leverage higher-order functions for reusability
- Implement proper error handling in modules
- Use composition over inheritance
- Apply separation of concerns
- Test modules independently
