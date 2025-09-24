# Authentication Fundamentals

## Introduction
Authentication is the process of verifying a user's identity, while authorization determines what resources a user can access. This lesson covers the fundamental concepts, different authentication methods, and security considerations for frontend applications.

## Authentication vs Authorization

### Understanding the Difference
```javascript
// Authentication: "Who are you?"
const authenticateUser = async (credentials) => {
    try {
        const response = await fetch('/api/auth/login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(credentials)
        });
        
        if (response.ok) {
            const user = await response.json();
            return { success: true, user };
        } else {
            return { success: false, error: 'Invalid credentials' };
        }
    } catch (error) {
        return { success: false, error: 'Authentication failed' };
    }
};

// Authorization: "What can you do?"
const checkPermission = (user, resource, action) => {
    const userRole = user.role;
    const permissions = {
        'admin': ['read', 'write', 'delete'],
        'editor': ['read', 'write'],
        'viewer': ['read']
    };
    
    return permissions[userRole]?.includes(action) || false;
};

// Usage example
const handleResourceAccess = async (resource, action) => {
    const user = getCurrentUser();
    
    if (!user) {
        redirectToLogin();
        return;
    }
    
    if (!checkPermission(user, resource, action)) {
        showError('Access denied');
        return;
    }
    
    // Proceed with authorized action
    performAction(resource, action);
};
```

**Code Explanation:**
- `authenticateUser(credentials)`: Verifies user identity using credentials
- `fetch('/api/auth/login', {...})`: Sends login request to authentication server
- `response.ok`: Checks if authentication was successful
- `checkPermission(user, resource, action)`: Determines if user can perform action
- `user.role`: Gets user's role for permission checking
- `permissions[userRole]?.includes(action)`: Checks if role has required permission
- `getCurrentUser()`: Retrieves currently authenticated user
- `redirectToLogin()`: Redirects unauthenticated users to login page
- `showError('Access denied')`: Displays authorization error message

**Benefits:**
- Clear separation between identity verification and access control
- Role-based permissions provide flexible authorization system
- Centralized permission checking prevents unauthorized access
- Proper error handling improves user experience

## Authentication Methods

### Password-Based Authentication
```javascript
// Secure password authentication
class PasswordAuth {
    constructor() {
        this.minPasswordLength = 8;
        this.passwordRequirements = {
            minLength: 8,
            requireUppercase: true,
            requireLowercase: true,
            requireNumbers: true,
            requireSpecialChars: true
        };
    }
    
    validatePassword(password) {
        const errors = [];
        
        if (password.length < this.passwordRequirements.minLength) {
            errors.push('Password must be at least 8 characters long');
        }
        
        if (this.passwordRequirements.requireUppercase && !/[A-Z]/.test(password)) {
            errors.push('Password must contain at least one uppercase letter');
        }
        
        if (this.passwordRequirements.requireLowercase && !/[a-z]/.test(password)) {
            errors.push('Password must contain at least one lowercase letter');
        }
        
        if (this.passwordRequirements.requireNumbers && !/\d/.test(password)) {
            errors.push('Password must contain at least one number');
        }
        
        if (this.passwordRequirements.requireSpecialChars && !/[!@#$%^&*(),.?":{}|<>]/.test(password)) {
            errors.push('Password must contain at least one special character');
        }
        
        return {
            isValid: errors.length === 0,
            errors
        };
    }
    
    async hashPassword(password) {
        // Note: In production, use a proper hashing library like bcrypt
        const encoder = new TextEncoder();
        const data = encoder.encode(password);
        const hashBuffer = await crypto.subtle.digest('SHA-256', data);
        const hashArray = Array.from(new Uint8Array(hashBuffer));
        return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
    }
    
    async login(email, password) {
        try {
            // Hash the password before sending (in production, this should be done server-side)
            const hashedPassword = await this.hashPassword(password);
            
            const response = await fetch('/api/auth/login', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    email,
                    password: hashedPassword
                })
            });
            
            if (response.ok) {
                const authData = await response.json();
                this.storeAuthData(authData);
                return { success: true, user: authData.user };
            } else {
                const error = await response.json();
                return { success: false, error: error.message };
            }
        } catch (error) {
            return { success: false, error: 'Login failed' };
        }
    }
    
    storeAuthData(authData) {
        // Store authentication data securely
        localStorage.setItem('auth_token', authData.token);
        localStorage.setItem('user_data', JSON.stringify(authData.user));
        localStorage.setItem('token_expiry', authData.expiresAt);
    }
    
    isTokenValid() {
        const token = localStorage.getItem('auth_token');
        const expiry = localStorage.getItem('token_expiry');
        
        if (!token || !expiry) {
            return false;
        }
        
        return new Date() < new Date(expiry);
    }
    
    logout() {
        localStorage.removeItem('auth_token');
        localStorage.removeItem('user_data');
        localStorage.removeItem('token_expiry');
    }
}
```

**Code Explanation:**
- `validatePassword(password)`: Checks password against security requirements
- `/[A-Z]/.test(password)`: Regular expression to check for uppercase letters
- `/[a-z]/.test(password)`: Regular expression to check for lowercase letters
- `/\d/.test(password)`: Regular expression to check for numbers
- `/[!@#$%^&*(),.?":{}|<>]/.test(password)`: Regular expression to check for special characters
- `crypto.subtle.digest('SHA-256', data)`: Web Crypto API for password hashing
- `Array.from(new Uint8Array(hashBuffer))`: Converts hash buffer to array
- `b.toString(16).padStart(2, '0')`: Converts bytes to hexadecimal
- `this.storeAuthData(authData)`: Securely stores authentication data
- `localStorage.setItem('auth_token', authData.token)`: Stores JWT token
- `new Date() < new Date(expiry)`: Checks if token is still valid
- `this.logout()`: Clears all stored authentication data

**Benefits:**
- Strong password validation prevents weak passwords
- Secure password hashing protects user credentials
- Token validation ensures session security
- Proper cleanup prevents data leakage

### Multi-Factor Authentication (MFA)
```javascript
// Multi-Factor Authentication implementation
class MFAHandler {
    constructor() {
        this.supportedMethods = ['totp', 'sms', 'email'];
    }
    
    async initiateMFA(userId, method = 'totp') {
        try {
            const response = await fetch('/api/auth/mfa/initiate', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    userId,
                    method
                })
            });
            
            if (response.ok) {
                const mfaData = await response.json();
                return {
                    success: true,
                    qrCode: mfaData.qrCode, // For TOTP setup
                    backupCodes: mfaData.backupCodes,
                    method
                };
            } else {
                throw new Error('Failed to initiate MFA');
            }
        } catch (error) {
            return { success: false, error: error.message };
        }
    }
    
    async verifyMFACode(userId, code, method = 'totp') {
        try {
            const response = await fetch('/api/auth/mfa/verify', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    userId,
                    code,
                    method
                })
            });
            
            if (response.ok) {
                const authData = await response.json();
                return { success: true, authData };
            } else {
                return { success: false, error: 'Invalid MFA code' };
            }
        } catch (error) {
            return { success: false, error: 'MFA verification failed' };
        }
    }
    
    async sendSMSCode(phoneNumber) {
        try {
            const response = await fetch('/api/auth/mfa/sms', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ phoneNumber })
            });
            
            return response.ok ? { success: true } : { success: false };
        } catch (error) {
            return { success: false, error: 'Failed to send SMS code' };
        }
    }
    
    async sendEmailCode(email) {
        try {
            const response = await fetch('/api/auth/mfa/email', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ email })
            });
            
            return response.ok ? { success: true } : { success: false };
        } catch (error) {
            return { success: false, error: 'Failed to send email code' };
        }
    }
}

// MFA React Component
function MFAVerification({ userId, method, onSuccess, onCancel }) {
    const [code, setCode] = useState('');
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState('');
    const mfaHandler = new MFAHandler();
    
    const handleVerifyCode = async (e) => {
        e.preventDefault();
        setIsLoading(true);
        setError('');
        
        const result = await mfaHandler.verifyMFACode(userId, code, method);
        
        if (result.success) {
            onSuccess(result.authData);
        } else {
            setError(result.error);
        }
        
        setIsLoading(false);
    };
    
    return (
        <div className="mfa-verification">
            <h2>Multi-Factor Authentication</h2>
            <p>Enter the verification code sent to your {method}</p>
            
            <form onSubmit={handleVerifyCode}>
                <input
                    type="text"
                    value={code}
                    onChange={(e) => setCode(e.target.value)}
                    placeholder="Enter verification code"
                    maxLength={6}
                    required
                />
                
                {error && <div className="error">{error}</div>}
                
                <div className="actions">
                    <button type="submit" disabled={isLoading}>
                        {isLoading ? 'Verifying...' : 'Verify'}
                    </button>
                    <button type="button" onClick={onCancel}>
                        Cancel
                    </button>
                </div>
            </form>
        </div>
    );
}
```

**Code Explanation:**
- `initiateMFA(userId, method)`: Starts MFA setup process
- `qrCode: mfaData.qrCode`: QR code for TOTP app setup
- `backupCodes: mfaData.backupCodes`: Recovery codes for account access
- `verifyMFACode(userId, code, method)`: Verifies MFA code from user
- `sendSMSCode(phoneNumber)`: Sends SMS verification code
- `sendEmailCode(email)`: Sends email verification code
- `useState('')`: React hook for managing component state
- `setIsLoading(true)`: Shows loading state during verification
- `setError(result.error)`: Displays error messages to user
- `maxLength={6}`: Limits code input to 6 characters
- `disabled={isLoading}`: Prevents multiple submissions

**Benefits:**
- Adds extra security layer beyond passwords
- Multiple verification methods provide flexibility
- Backup codes ensure account recovery
- Real-time verification improves user experience

## Security Best Practices

### Input Validation and Sanitization
```javascript
// Secure input handling
class InputValidator {
    constructor() {
        this.emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        this.usernameRegex = /^[a-zA-Z0-9_]{3,20}$/;
        this.xssPatterns = [
            /<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi,
            /javascript:/gi,
            /on\w+\s*=/gi
        ];
    }
    
    sanitizeInput(input) {
        if (typeof input !== 'string') {
            return input;
        }
        
        // Remove XSS patterns
        let sanitized = input;
        this.xssPatterns.forEach(pattern => {
            sanitized = sanitized.replace(pattern, '');
        });
        
        // HTML entity encoding
        sanitized = sanitized
            .replace(/&/g, '&amp;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&#x27;');
        
        return sanitized;
    }
    
    validateEmail(email) {
        return this.emailRegex.test(email);
    }
    
    validateUsername(username) {
        return this.usernameRegex.test(username);
    }
    
    validateInput(input, type) {
        const sanitized = this.sanitizeInput(input);
        
        switch (type) {
            case 'email':
                return {
                    isValid: this.validateEmail(sanitized),
                    value: sanitized,
                    error: this.validateEmail(sanitized) ? null : 'Invalid email format'
                };
            case 'username':
                return {
                    isValid: this.validateUsername(sanitized),
                    value: sanitized,
                    error: this.validateUsername(sanitized) ? null : 'Username must be 3-20 characters, letters, numbers, and underscores only'
                };
            default:
                return {
                    isValid: true,
                    value: sanitized,
                    error: null
                };
        }
    }
}

// CSRF Protection
class CSRFProtection {
    constructor() {
        this.csrfToken = null;
    }
    
    async getCSRFToken() {
        try {
            const response = await fetch('/api/csrf-token');
            if (response.ok) {
                const data = await response.json();
                this.csrfToken = data.token;
                return this.csrfToken;
            }
        } catch (error) {
            console.error('Failed to get CSRF token:', error);
        }
        return null;
    }
    
    async makeAuthenticatedRequest(url, options = {}) {
        const token = await this.getCSRFToken();
        
        const headers = {
            'Content-Type': 'application/json',
            'X-CSRF-Token': token,
            ...options.headers
        };
        
        return fetch(url, {
            ...options,
            headers
        });
    }
}
```

**Code Explanation:**
- `emailRegex`: Regular expression for email validation
- `usernameRegex`: Regular expression for username validation
- `xssPatterns`: Array of patterns to detect XSS attacks
- `sanitizeInput(input)`: Removes potentially dangerous content
- `replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '')`: Removes script tags
- `replace(/javascript:/gi, '')`: Removes javascript: protocols
- `replace(/on\w+\s*=/gi, '')`: Removes event handlers
- `replace(/&/g, '&amp;')`: HTML entity encoding for ampersands
- `replace(/</g, '&lt;')`: HTML entity encoding for less-than
- `validateInput(input, type)`: Validates and sanitizes input based on type
- `getCSRFToken()`: Retrieves CSRF token from server
- `X-CSRF-Token`: Header for CSRF protection
- `makeAuthenticatedRequest()`: Makes secure requests with CSRF protection

**Benefits:**
- Prevents XSS attacks through input sanitization
- Validates input format to prevent injection attacks
- CSRF protection prevents cross-site request forgery
- HTML entity encoding prevents script execution
- Comprehensive validation improves data integrity

## Authentication State Management

### React Context for Auth State
```javascript
// Authentication Context
import React, { createContext, useContext, useReducer, useEffect } from 'react';

const AuthContext = createContext();

const authReducer = (state, action) => {
    switch (action.type) {
        case 'LOGIN_SUCCESS':
            return {
                ...state,
                isAuthenticated: true,
                user: action.payload.user,
                token: action.payload.token,
                isLoading: false
            };
        case 'LOGIN_FAILURE':
            return {
                ...state,
                isAuthenticated: false,
                user: null,
                token: null,
                error: action.payload.error,
                isLoading: false
            };
        case 'LOGOUT':
            return {
                ...state,
                isAuthenticated: false,
                user: null,
                token: null,
                error: null,
                isLoading: false
            };
        case 'SET_LOADING':
            return {
                ...state,
                isLoading: action.payload
            };
        case 'CLEAR_ERROR':
            return {
                ...state,
                error: null
            };
        default:
            return state;
    }
};

const initialState = {
    isAuthenticated: false,
    user: null,
    token: null,
    error: null,
    isLoading: false
};

export const AuthProvider = ({ children }) => {
    const [state, dispatch] = useReducer(authReducer, initialState);
    
    useEffect(() => {
        // Check for existing authentication on app load
        const token = localStorage.getItem('auth_token');
        const userData = localStorage.getItem('user_data');
        
        if (token && userData) {
            try {
                const user = JSON.parse(userData);
                dispatch({
                    type: 'LOGIN_SUCCESS',
                    payload: { user, token }
                });
            } catch (error) {
                // Clear invalid data
                localStorage.removeItem('auth_token');
                localStorage.removeItem('user_data');
            }
        }
    }, []);
    
    const login = async (credentials) => {
        dispatch({ type: 'SET_LOADING', payload: true });
        
        try {
            const response = await fetch('/api/auth/login', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(credentials)
            });
            
            if (response.ok) {
                const authData = await response.json();
                
                // Store authentication data
                localStorage.setItem('auth_token', authData.token);
                localStorage.setItem('user_data', JSON.stringify(authData.user));
                
                dispatch({
                    type: 'LOGIN_SUCCESS',
                    payload: authData
                });
                
                return { success: true };
            } else {
                const error = await response.json();
                dispatch({
                    type: 'LOGIN_FAILURE',
                    payload: { error: error.message }
                });
                return { success: false, error: error.message };
            }
        } catch (error) {
            dispatch({
                type: 'LOGIN_FAILURE',
                payload: { error: 'Login failed' }
            });
            return { success: false, error: 'Login failed' };
        }
    };
    
    const logout = () => {
        localStorage.removeItem('auth_token');
        localStorage.removeItem('user_data');
        dispatch({ type: 'LOGOUT' });
    };
    
    const clearError = () => {
        dispatch({ type: 'CLEAR_ERROR' });
    };
    
    const value = {
        ...state,
        login,
        logout,
        clearError
    };
    
    return (
        <AuthContext.Provider value={value}>
            {children}
        </AuthContext.Provider>
    );
};

export const useAuth = () => {
    const context = useContext(AuthContext);
    if (!context) {
        throw new Error('useAuth must be used within an AuthProvider');
    }
    return context;
};

// Protected Route Component
export const ProtectedRoute = ({ children, requiredRole }) => {
    const { isAuthenticated, user } = useAuth();
    
    if (!isAuthenticated) {
        return <Navigate to="/login" replace />;
    }
    
    if (requiredRole && user.role !== requiredRole) {
        return <Navigate to="/unauthorized" replace />;
    }
    
    return children;
};
```

**Code Explanation:**
- `createContext()`: Creates React context for authentication state
- `useReducer(authReducer, initialState)`: Manages complex state with reducer pattern
- `LOGIN_SUCCESS`: Action type for successful authentication
- `LOGIN_FAILURE`: Action type for failed authentication
- `LOGOUT`: Action type for user logout
- `SET_LOADING`: Action type for loading state management
- `useEffect(() => {...}, [])`: Checks for existing authentication on app load
- `localStorage.getItem('auth_token')`: Retrieves stored authentication token
- `JSON.parse(userData)`: Parses stored user data
- `dispatch({ type: 'LOGIN_SUCCESS', payload: authData })`: Updates state with auth data
- `localStorage.setItem('auth_token', authData.token)`: Stores authentication token
- `localStorage.removeItem('auth_token')`: Removes authentication token on logout
- `useAuth()`: Custom hook to access authentication context
- `ProtectedRoute`: Component that protects routes based on authentication
- `requiredRole`: Optional role requirement for route access
- `<Navigate to="/login" replace />`: Redirects unauthenticated users

**Benefits:**
- Centralized authentication state management
- Automatic token persistence across browser sessions
- Role-based route protection
- Clean separation of authentication logic
- Easy to use throughout the application
- Proper error handling and loading states
