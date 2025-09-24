# RESTful API Consumption

## Introduction
RESTful APIs are the standard for web service communication. This lesson covers HTTP methods, status codes, request configuration, and response handling patterns for effective API integration.

## HTTP Methods and Status Codes

### HTTP Methods Overview
```javascript
// GET - Retrieve data
async function fetchUsers() {
    const response = await fetch('/api/users');
    const users = await response.json();
    return users;
}

// POST - Create new resource
async function createUser(userData) {
    const response = await fetch('/api/users', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(userData)
    });
    const newUser = await response.json();
    return newUser;
}

// PUT - Update entire resource
async function updateUser(userId, userData) {
    const response = await fetch(`/api/users/${userId}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(userData)
    });
    const updatedUser = await response.json();
    return updatedUser;
}

// PATCH - Partial update
async function updateUserEmail(userId, email) {
    const response = await fetch(`/api/users/${userId}`, {
        method: 'PATCH',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({ email })
    });
    const updatedUser = await response.json();
    return updatedUser;
}

// DELETE - Remove resource
async function deleteUser(userId) {
    const response = await fetch(`/api/users/${userId}`, {
        method: 'DELETE'
    });
    return response.ok;
}
```

**Code Explanation:**
- `async function`: Declares asynchronous function that can use await
- `fetch('/api/users')`: Makes GET request to retrieve users (default method)
- `await response.json()`: Parses response body as JSON
- `method: 'POST'`: Specifies HTTP method for creating new resources
- `headers: { 'Content-Type': 'application/json' }`: Sets content type header for JSON data
- `body: JSON.stringify(userData)`: Converts JavaScript object to JSON string for request body
- `method: 'PUT'`: Updates entire resource (replaces all fields)
- `method: 'PATCH'`: Partially updates resource (only specified fields)
- `method: 'DELETE'`: Removes resource from server
- `response.ok`: Boolean indicating if response status is successful (200-299)
- `\`/api/users/${userId}\``: Template literal for dynamic URL construction

**Benefits:**
- GET requests are idempotent and cacheable
- POST creates new resources with proper data validation
- PUT provides complete resource replacement
- PATCH enables efficient partial updates
- DELETE removes resources cleanly
- Proper HTTP methods follow REST conventions

### Status Code Handling
```javascript
// Comprehensive status code handling
async function handleApiResponse(response) {
    if (response.ok) {
        return await response.json();
    }
    
    switch (response.status) {
        case 400:
            throw new Error('Bad Request - Invalid data provided');
        case 401:
            throw new Error('Unauthorized - Authentication required');
        case 403:
            throw new Error('Forbidden - Access denied');
        case 404:
            throw new Error('Not Found - Resource does not exist');
        case 409:
            throw new Error('Conflict - Resource already exists');
        case 422:
            throw new Error('Unprocessable Entity - Validation failed');
        case 429:
            throw new Error('Too Many Requests - Rate limit exceeded');
        case 500:
            throw new Error('Internal Server Error - Server error');
        case 502:
            throw new Error('Bad Gateway - Server unavailable');
        case 503:
            throw new Error('Service Unavailable - Server overloaded');
        default:
            throw new Error(`HTTP Error ${response.status}: ${response.statusText}`);
    }
}

// Usage example
async function fetchUserWithErrorHandling(userId) {
    try {
        const response = await fetch(`/api/users/${userId}`);
        return await handleApiResponse(response);
    } catch (error) {
        console.error('Error fetching user:', error.message);
        throw error;
    }
}
```

## Request Configuration and Headers

### Advanced Request Configuration
```javascript
// Comprehensive request configuration
class ApiClient {
    constructor(baseURL, defaultHeaders = {}) {
        this.baseURL = baseURL;
        this.defaultHeaders = {
            'Content-Type': 'application/json',
            ...defaultHeaders
        };
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        const config = {
            headers: {
                ...this.defaultHeaders,
                ...options.headers
            },
            ...options
        };
        
        try {
            const response = await fetch(url, config);
            return await this.handleResponse(response);
        } catch (error) {
            console.error('API request failed:', error);
            throw error;
        }
    }
    
    async handleResponse(response) {
        if (!response.ok) {
            const errorData = await response.json().catch(() => ({}));
            throw new Error(errorData.message || `HTTP ${response.status}`);
        }
        
        const contentType = response.headers.get('content-type');
        if (contentType && contentType.includes('application/json')) {
            return await response.json();
        }
        
        return await response.text();
    }
    
    // Convenience methods
    get(endpoint, options = {}) {
        return this.request(endpoint, { method: 'GET', ...options });
    }
    
    post(endpoint, data, options = {}) {
        return this.request(endpoint, {
            method: 'POST',
            body: JSON.stringify(data),
            ...options
        });
    }
    
    put(endpoint, data, options = {}) {
        return this.request(endpoint, {
            method: 'PUT',
            body: JSON.stringify(data),
            ...options
        });
    }
    
    patch(endpoint, data, options = {}) {
        return this.request(endpoint, {
            method: 'PATCH',
            body: JSON.stringify(data),
            ...options
        });
    }
    
    delete(endpoint, options = {}) {
        return this.request(endpoint, { method: 'DELETE', ...options });
    }
}

// Usage example
const apiClient = new ApiClient('https://api.example.com', {
    'Authorization': 'Bearer your-token-here',
    'X-API-Version': 'v1'
});

// Using the API client
async function fetchUsers() {
    return await apiClient.get('/users');
}

async function createUser(userData) {
    return await apiClient.post('/users', userData);
}
```

### Authentication Headers
```javascript
// Token-based authentication
class AuthenticatedApiClient extends ApiClient {
    constructor(baseURL, token) {
        super(baseURL, {
            'Authorization': `Bearer ${token}`
        });
        this.token = token;
    }
    
    setToken(token) {
        this.token = token;
        this.defaultHeaders['Authorization'] = `Bearer ${token}`;
    }
    
    async refreshToken() {
        try {
            const response = await this.post('/auth/refresh');
            this.setToken(response.token);
            return response.token;
        } catch (error) {
            // Redirect to login or handle token refresh failure
            this.handleAuthError();
            throw error;
        }
    }
    
    handleAuthError() {
        // Clear token and redirect to login
        this.setToken(null);
        window.location.href = '/login';
    }
}

// API Key authentication
class ApiKeyClient extends ApiClient {
    constructor(baseURL, apiKey) {
        super(baseURL, {
            'X-API-Key': apiKey
        });
    }
}

// Basic authentication
class BasicAuthClient extends ApiClient {
    constructor(baseURL, username, password) {
        const credentials = btoa(`${username}:${password}`);
        super(baseURL, {
            'Authorization': `Basic ${credentials}`
        });
    }
}
```

## Response Handling Patterns

### Data Transformation and Normalization
```javascript
// Data transformation utilities
class DataTransformer {
    static normalizeUser(user) {
        return {
            id: user.id,
            name: user.full_name || user.name,
            email: user.email_address || user.email,
            avatar: user.profile_image || user.avatar_url,
            createdAt: new Date(user.created_at),
            updatedAt: new Date(user.updated_at)
        };
    }
    
    static normalizeUsers(users) {
        return users.map(user => this.normalizeUser(user));
    }
    
    static transformApiResponse(response) {
        return {
            data: response.data,
            meta: {
                total: response.total_count,
                page: response.current_page,
                perPage: response.per_page,
                totalPages: response.total_pages
            },
            links: {
                first: response.first_page_url,
                last: response.last_page_url,
                next: response.next_page_url,
                prev: response.prev_page_url
            }
        };
    }
}

// Usage with API client
async function fetchUsersWithTransformation() {
    const response = await apiClient.get('/users');
    const normalizedResponse = DataTransformer.transformApiResponse(response);
    const normalizedUsers = DataTransformer.normalizeUsers(normalizedResponse.data);
    
    return {
        users: normalizedUsers,
        pagination: normalizedResponse.meta,
        links: normalizedResponse.links
    };
}
```

### Error Response Parsing
```javascript
// Comprehensive error handling
class ApiError extends Error {
    constructor(message, status, data = null) {
        super(message);
        this.name = 'ApiError';
        this.status = status;
        this.data = data;
    }
}

async function handleApiError(response) {
    let errorData = {};
    
    try {
        errorData = await response.json();
    } catch {
        // Response is not JSON
    }
    
    const message = errorData.message || errorData.error || `HTTP ${response.status}`;
    throw new ApiError(message, response.status, errorData);
}

// Validation error handling
function parseValidationErrors(errorData) {
    if (errorData.errors) {
        return Object.entries(errorData.errors).map(([field, messages]) => ({
            field,
            messages: Array.isArray(messages) ? messages : [messages]
        }));
    }
    return [];
}

// Usage example
async function createUserWithValidation(userData) {
    try {
        const response = await apiClient.post('/users', userData);
        return response;
    } catch (error) {
        if (error.status === 422) {
            const validationErrors = parseValidationErrors(error.data);
            throw new Error(`Validation failed: ${validationErrors.map(e => e.messages.join(', ')).join('; ')}`);
        }
        throw error;
    }
}
```

### Loading States and Optimistic Updates
```javascript
// Loading state management
class LoadingManager {
    constructor() {
        this.loadingStates = new Map();
    }
    
    setLoading(key, isLoading) {
        this.loadingStates.set(key, isLoading);
    }
    
    isLoading(key) {
        return this.loadingStates.get(key) || false;
    }
    
    clearLoading(key) {
        this.loadingStates.delete(key);
    }
}

// Optimistic updates
class OptimisticUpdater {
    constructor(apiClient) {
        this.apiClient = apiClient;
        this.pendingUpdates = new Map();
    }
    
    async optimisticUpdate(key, updateFn, apiCall) {
        // Store original state
        const originalState = this.getCurrentState(key);
        
        // Apply optimistic update
        const optimisticState = updateFn(originalState);
        this.updateState(key, optimisticState);
        
        // Store pending update
        this.pendingUpdates.set(key, {
            originalState,
            optimisticState,
            apiCall
        });
        
        try {
            // Make API call
            const result = await apiCall();
            this.updateState(key, result);
            this.pendingUpdates.delete(key);
            return result;
        } catch (error) {
            // Rollback on error
            this.updateState(key, originalState);
            this.pendingUpdates.delete(key);
            throw error;
        }
    }
    
    rollbackAll() {
        for (const [key, { originalState }] of this.pendingUpdates) {
            this.updateState(key, originalState);
        }
        this.pendingUpdates.clear();
    }
}

// Usage example
const loadingManager = new LoadingManager();
const optimisticUpdater = new OptimisticUpdater(apiClient);

async function updateUserOptimistically(userId, updates) {
    const key = `user-${userId}`;
    
    loadingManager.setLoading(key, true);
    
    try {
        const result = await optimisticUpdater.optimisticUpdate(
            key,
            (currentUser) => ({ ...currentUser, ...updates }),
            () => apiClient.patch(`/users/${userId}`, updates)
        );
        
        return result;
    } catch (error) {
        console.error('Optimistic update failed:', error);
        throw error;
    } finally {
        loadingManager.setLoading(key, false);
    }
}
```

## Practical Examples

### Complete API Integration Example
```javascript
// Complete user management API integration
class UserApiService {
    constructor(apiClient) {
        this.apiClient = apiClient;
        this.cache = new Map();
    }
    
    async getUsers(params = {}) {
        const queryString = new URLSearchParams(params).toString();
        const endpoint = `/users${queryString ? `?${queryString}` : ''}`;
        
        try {
            const response = await this.apiClient.get(endpoint);
            const normalizedUsers = DataTransformer.normalizeUsers(response.data);
            
            // Cache individual users
            normalizedUsers.forEach(user => {
                this.cache.set(`user-${user.id}`, user);
            });
            
            return {
                users: normalizedUsers,
                pagination: response.meta,
                links: response.links
            };
        } catch (error) {
            console.error('Error fetching users:', error);
            throw error;
        }
    }
    
    async getUser(userId) {
        const cacheKey = `user-${userId}`;
        
        // Check cache first
        if (this.cache.has(cacheKey)) {
            return this.cache.get(cacheKey);
        }
        
        try {
            const response = await this.apiClient.get(`/users/${userId}`);
            const normalizedUser = DataTransformer.normalizeUser(response);
            
            // Cache the user
            this.cache.set(cacheKey, normalizedUser);
            
            return normalizedUser;
        } catch (error) {
            console.error(`Error fetching user ${userId}:`, error);
            throw error;
        }
    }
    
    async createUser(userData) {
        try {
            const response = await this.apiClient.post('/users', userData);
            const normalizedUser = DataTransformer.normalizeUser(response);
            
            // Add to cache
            this.cache.set(`user-${normalizedUser.id}`, normalizedUser);
            
            return normalizedUser;
        } catch (error) {
            console.error('Error creating user:', error);
            throw error;
        }
    }
    
    async updateUser(userId, updates) {
        try {
            const response = await this.apiClient.patch(`/users/${userId}`, updates);
            const normalizedUser = DataTransformer.normalizeUser(response);
            
            // Update cache
            this.cache.set(`user-${userId}`, normalizedUser);
            
            return normalizedUser;
        } catch (error) {
            console.error(`Error updating user ${userId}:`, error);
            throw error;
        }
    }
    
    async deleteUser(userId) {
        try {
            await this.apiClient.delete(`/users/${userId}`);
            
            // Remove from cache
            this.cache.delete(`user-${userId}`);
            
            return true;
        } catch (error) {
            console.error(`Error deleting user ${userId}:`, error);
            throw error;
        }
    }
    
    // Search users
    async searchUsers(query, filters = {}) {
        const params = {
            search: query,
            ...filters
        };
        
        return await this.getUsers(params);
    }
    
    // Batch operations
    async batchUpdateUsers(updates) {
        try {
            const response = await this.apiClient.post('/users/batch-update', {
                updates
            });
            
            // Update cache for all affected users
            response.updated_users.forEach(user => {
                const normalizedUser = DataTransformer.normalizeUser(user);
                this.cache.set(`user-${normalizedUser.id}`, normalizedUser);
            });
            
            return response;
        } catch (error) {
            console.error('Error in batch update:', error);
            throw error;
        }
    }
}

// Usage example
const userApiService = new UserApiService(apiClient);

// Fetch users with pagination
const usersPage = await userApiService.getUsers({
    page: 1,
    per_page: 10,
    sort: 'name',
    order: 'asc'
});

// Search users
const searchResults = await userApiService.searchUsers('john', {
    role: 'admin',
    status: 'active'
});
```

## Exercise: API Integration Project

Create a comprehensive API integration system with:
- RESTful API client with authentication
- Error handling and retry mechanisms
- Data transformation and normalization
- Caching and optimistic updates
- Loading states and user feedback
- Batch operations and search functionality

## Key Takeaways
- Use appropriate HTTP methods for different operations
- Handle status codes properly for better user experience
- Implement proper authentication headers
- Transform API responses to match your application's data structure
- Use optimistic updates for better perceived performance
- Cache data to reduce API calls and improve performance
- Provide clear error messages to users
- Implement retry mechanisms for failed requests
