# Modern Data Fetching Approaches

## Introduction
Modern web applications require sophisticated data fetching strategies. This lesson covers Fetch API, third-party libraries, caching strategies, and performance optimization for data handling.

## Fetch API Advanced Usage

### Advanced Fetch Patterns
```javascript
// Enhanced fetch wrapper with error handling
class ApiClient {
    constructor(baseURL, options = {}) {
        this.baseURL = baseURL;
        this.defaultOptions = {
            headers: {
                'Content-Type': 'application/json',
                ...options.headers
            },
            ...options
        };
    }
    
    async request(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        const config = {
            ...this.defaultOptions,
            ...options,
            headers: {
                ...this.defaultOptions.headers,
                ...options.headers
            }
        };
        
        try {
            const response = await fetch(url, config);
            
            if (!response.ok) {
                throw new ApiError(
                    `HTTP ${response.status}: ${response.statusText}`,
                    response.status,
                    await this.parseResponse(response)
                );
            }
            
            return await this.parseResponse(response);
        } catch (error) {
            if (error instanceof ApiError) {
                throw error;
            }
            throw new ApiError(`Network error: ${error.message}`, 0);
        }
    }
    
    async parseResponse(response) {
        const contentType = response.headers.get('content-type');
        
        if (contentType && contentType.includes('application/json')) {
            return await response.json();
        }
        
        if (contentType && contentType.includes('text/')) {
            return await response.text();
        }
        
        return await response.blob();
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

// Custom error class
class ApiError extends Error {
    constructor(message, status, data = null) {
        super(message);
        this.name = 'ApiError';
        this.status = status;
        this.data = data;
    }
}

// Usage
const apiClient = new ApiClient('https://api.example.com');

// GET request
const users = await apiClient.get('/users');

// POST request
const newUser = await apiClient.post('/users', {
    name: 'John Doe',
    email: 'john@example.com'
});
```

### Request Cancellation and Timeouts
```javascript
// Request cancellation with AbortController
class CancellableApiClient extends ApiClient {
    constructor(baseURL, options = {}) {
        super(baseURL, options);
        this.activeRequests = new Map();
    }
    
    async request(endpoint, options = {}) {
        const controller = new AbortController();
        const requestId = `${options.method || 'GET'}:${endpoint}`;
        
        // Store active request
        this.activeRequests.set(requestId, controller);
        
        // Set timeout
        const timeoutId = setTimeout(() => {
            controller.abort();
        }, options.timeout || 10000);
        
        try {
            const result = await super.request(endpoint, {
                ...options,
                signal: controller.signal
            });
            
            clearTimeout(timeoutId);
            this.activeRequests.delete(requestId);
            return result;
        } catch (error) {
            clearTimeout(timeoutId);
            this.activeRequests.delete(requestId);
            
            if (error.name === 'AbortError') {
                throw new ApiError('Request was cancelled or timed out', 0);
            }
            throw error;
        }
    }
    
    cancelRequest(method, endpoint) {
        const requestId = `${method}:${endpoint}`;
        const controller = this.activeRequests.get(requestId);
        
        if (controller) {
            controller.abort();
            this.activeRequests.delete(requestId);
        }
    }
    
    cancelAllRequests() {
        this.activeRequests.forEach(controller => controller.abort());
        this.activeRequests.clear();
    }
}

// Usage with cancellation
const cancellableClient = new CancellableApiClient('https://api.example.com');

// Start a request
const userPromise = cancellableClient.get('/users');

// Cancel the request if needed
setTimeout(() => {
    cancellableClient.cancelRequest('GET', '/users');
}, 5000);

try {
    const users = await userPromise;
    console.log('Users:', users);
} catch (error) {
    if (error.message.includes('cancelled')) {
        console.log('Request was cancelled');
    }
}
```

### Streaming Response Handling
```javascript
// Streaming response handler
class StreamingApiClient extends ApiClient {
    async streamRequest(endpoint, options = {}) {
        const url = `${this.baseURL}${endpoint}`;
        const config = {
            ...this.defaultOptions,
            ...options,
            headers: {
                ...this.defaultOptions.headers,
                ...options.headers
            }
        };
        
        const response = await fetch(url, config);
        
        if (!response.ok) {
            throw new ApiError(
                `HTTP ${response.status}: ${response.statusText}`,
                response.status
            );
        }
        
        return response.body;
    }
    
    async processStream(stream, processor) {
        const reader = stream.getReader();
        const decoder = new TextDecoder();
        
        try {
            while (true) {
                const { done, value } = await reader.read();
                
                if (done) break;
                
                const chunk = decoder.decode(value, { stream: true });
                await processor(chunk);
            }
        } finally {
            reader.releaseLock();
        }
    }
}

// Usage for streaming data
const streamingClient = new StreamingApiClient('https://api.example.com');

async function processLargeDataset() {
    const stream = await streamingClient.streamRequest('/large-dataset');
    
    await streamingClient.processStream(stream, async (chunk) => {
        // Process each chunk
        const data = JSON.parse(chunk);
        console.log('Received chunk:', data);
        
        // Update UI with progress
        updateProgress(data.progress);
    });
}
```

## Third-Party Libraries Comparison

### Axios Implementation
```javascript
import axios from 'axios';

// Axios instance configuration
const axiosInstance = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 10000,
    headers: {
        'Content-Type': 'application/json'
    }
});

// Request interceptor
axiosInstance.interceptors.request.use(
    (config) => {
        // Add auth token
        const token = localStorage.getItem('authToken');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        
        // Log request
        console.log(`Making ${config.method.toUpperCase()} request to ${config.url}`);
        
        return config;
    },
    (error) => {
        return Promise.reject(error);
    }
);

// Response interceptor
axiosInstance.interceptors.response.use(
    (response) => {
        // Log response
        console.log(`Received response from ${response.config.url}`);
        return response;
    },
    (error) => {
        // Handle common errors
        if (error.response?.status === 401) {
            // Redirect to login
            window.location.href = '/login';
        }
        
        if (error.response?.status === 429) {
            // Handle rate limiting
            console.warn('Rate limited, retrying...');
        }
        
        return Promise.reject(error);
    }
);

// Usage
class AxiosApiService {
    async getUsers() {
        try {
            const response = await axiosInstance.get('/users');
            return response.data;
        } catch (error) {
            throw new Error(`Failed to fetch users: ${error.message}`);
        }
    }
    
    async createUser(userData) {
        try {
            const response = await axiosInstance.post('/users', userData);
            return response.data;
        } catch (error) {
            throw new Error(`Failed to create user: ${error.message}`);
        }
    }
    
    async updateUser(userId, userData) {
        try {
            const response = await axiosInstance.patch(`/users/${userId}`, userData);
            return response.data;
        } catch (error) {
            throw new Error(`Failed to update user: ${error.message}`);
        }
    }
}
```

### SWR Implementation
```javascript
import useSWR from 'swr';

// SWR fetcher function
const fetcher = async (url) => {
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error('Failed to fetch data');
    }
    return response.json();
};

// Custom hook for user data
function useUser(userId) {
    const { data, error, mutate } = useSWR(
        userId ? `/api/users/${userId}` : null,
        fetcher,
        {
            revalidateOnFocus: false,
            revalidateOnReconnect: true,
            dedupingInterval: 60000, // 1 minute
            errorRetryCount: 3,
            errorRetryInterval: 5000
        }
    );
    
    return {
        user: data,
        isLoading: !error && !data,
        isError: error,
        mutate
    };
}

// Custom hook for users list
function useUsers() {
    const { data, error, mutate } = useSWR('/api/users', fetcher, {
        revalidateOnFocus: true,
        refreshInterval: 30000 // 30 seconds
    });
    
    return {
        users: data || [],
        isLoading: !error && !data,
        isError: error,
        mutate
    };
}

// Usage in component
function UserProfile({ userId }) {
    const { user, isLoading, isError, mutate } = useUser(userId);
    
    if (isLoading) return <div>Loading...</div>;
    if (isError) return <div>Error loading user</div>;
    
    const handleUpdate = async (userData) => {
        try {
            await updateUser(userId, userData);
            mutate(); // Revalidate data
        } catch (error) {
            console.error('Failed to update user:', error);
        }
    };
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
            <button onClick={() => handleUpdate({ name: 'New Name' })}>
                Update Name
            </button>
        </div>
    );
}
```

### React Query Implementation
```javascript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Query keys
const queryKeys = {
    users: ['users'],
    user: (id) => ['users', id],
    userPosts: (id) => ['users', id, 'posts']
};

// API functions
const api = {
    getUsers: () => fetch('/api/users').then(res => res.json()),
    getUser: (id) => fetch(`/api/users/${id}`).then(res => res.json()),
    createUser: (userData) => fetch('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(userData)
    }).then(res => res.json()),
    updateUser: (id, userData) => fetch(`/api/users/${id}`, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(userData)
    }).then(res => res.json()),
    deleteUser: (id) => fetch(`/api/users/${id}`, { method: 'DELETE' })
};

// Custom hooks
function useUsers() {
    return useQuery({
        queryKey: queryKeys.users,
        queryFn: api.getUsers,
        staleTime: 5 * 60 * 1000, // 5 minutes
        cacheTime: 10 * 60 * 1000 // 10 minutes
    });
}

function useUser(userId) {
    return useQuery({
        queryKey: queryKeys.user(userId),
        queryFn: () => api.getUser(userId),
        enabled: !!userId,
        staleTime: 5 * 60 * 1000
    });
}

function useCreateUser() {
    const queryClient = useQueryClient();
    
    return useMutation({
        mutationFn: api.createUser,
        onSuccess: () => {
            queryClient.invalidateQueries({ queryKey: queryKeys.users });
        }
    });
}

function useUpdateUser() {
    const queryClient = useQueryClient();
    
    return useMutation({
        mutationFn: ({ id, userData }) => api.updateUser(id, userData),
        onSuccess: (data, variables) => {
            queryClient.invalidateQueries({ queryKey: queryKeys.users });
            queryClient.invalidateQueries({ queryKey: queryKeys.user(variables.id) });
        }
    });
}

// Usage in component
function UserManagement() {
    const { data: users, isLoading, error } = useUsers();
    const createUserMutation = useCreateUser();
    const updateUserMutation = useUpdateUser();
    
    const handleCreateUser = async (userData) => {
        try {
            await createUserMutation.mutateAsync(userData);
            console.log('User created successfully');
        } catch (error) {
            console.error('Failed to create user:', error);
        }
    };
    
    const handleUpdateUser = async (userId, userData) => {
        try {
            await updateUserMutation.mutateAsync({ id: userId, userData });
            console.log('User updated successfully');
        } catch (error) {
            console.error('Failed to update user:', error);
        }
    };
    
    if (isLoading) return <div>Loading...</div>;
    if (error) return <div>Error: {error.message}</div>;
    
    return (
        <div>
            <h2>Users</h2>
            {users.map(user => (
                <div key={user.id}>
                    <span>{user.name}</span>
                    <button onClick={() => handleUpdateUser(user.id, { name: 'Updated' })}>
                        Update
                    </button>
                </div>
            ))}
        </div>
    );
}
```

## State Management for API Data

### Server State vs Client State
```javascript
// Server state management
class ServerStateManager {
    constructor() {
        this.cache = new Map();
        this.subscribers = new Map();
        this.refetchTimers = new Map();
    }
    
    // Cache server data
    setCache(key, data, ttl = 300000) { // 5 minutes default TTL
        this.cache.set(key, {
            data,
            timestamp: Date.now(),
            ttl
        });
        
        // Notify subscribers
        this.notifySubscribers(key, data);
        
        // Set refetch timer
        this.setRefetchTimer(key, ttl);
    }
    
    // Get cached data
    getCache(key) {
        const cached = this.cache.get(key);
        
        if (!cached) return null;
        
        const isExpired = Date.now() - cached.timestamp > cached.ttl;
        
        if (isExpired) {
            this.cache.delete(key);
            return null;
        }
        
        return cached.data;
    }
    
    // Subscribe to cache updates
    subscribe(key, callback) {
        if (!this.subscribers.has(key)) {
            this.subscribers.set(key, new Set());
        }
        this.subscribers.get(key).add(callback);
        
        // Return unsubscribe function
        return () => {
            const subscribers = this.subscribers.get(key);
            if (subscribers) {
                subscribers.delete(callback);
                if (subscribers.size === 0) {
                    this.subscribers.delete(key);
                }
            }
        };
    }
    
    // Notify subscribers
    notifySubscribers(key, data) {
        const subscribers = this.subscribers.get(key);
        if (subscribers) {
            subscribers.forEach(callback => callback(data));
        }
    }
    
    // Set refetch timer
    setRefetchTimer(key, ttl) {
        if (this.refetchTimers.has(key)) {
            clearTimeout(this.refetchTimers.get(key));
        }
        
        const timer = setTimeout(() => {
            this.refetch(key);
        }, ttl);
        
        this.refetchTimers.set(key, timer);
    }
    
    // Refetch data
    async refetch(key) {
        // This would trigger a refetch of the data
        console.log(`Refetching data for key: ${key}`);
    }
}

// Client state management
class ClientStateManager {
    constructor() {
        this.state = new Map();
        this.subscribers = new Map();
    }
    
    // Set client state
    setState(key, value) {
        this.state.set(key, value);
        this.notifySubscribers(key, value);
    }
    
    // Get client state
    getState(key) {
        return this.state.get(key);
    }
    
    // Subscribe to state changes
    subscribe(key, callback) {
        if (!this.subscribers.has(key)) {
            this.subscribers.set(key, new Set());
        }
        this.subscribers.get(key).add(callback);
        
        return () => {
            const subscribers = this.subscribers.get(key);
            if (subscribers) {
                subscribers.delete(callback);
                if (subscribers.size === 0) {
                    this.subscribers.delete(key);
                }
            }
        };
    }
    
    // Notify subscribers
    notifySubscribers(key, value) {
        const subscribers = this.subscribers.get(key);
        if (subscribers) {
            subscribers.forEach(callback => callback(value));
        }
    }
}

// Usage
const serverStateManager = new ServerStateManager();
const clientStateManager = new ClientStateManager();

// Server state
serverStateManager.setCache('users', [{ id: 1, name: 'John' }]);
const users = serverStateManager.getCache('users');

// Client state
clientStateManager.setState('isLoading', false);
clientStateManager.setState('selectedUser', null);
```

## Exercise: Modern Data Fetching Application

Create a data fetching application with:
- Advanced Fetch API implementation
- Request cancellation and timeout handling
- SWR or React Query integration
- Server and client state management
- Error handling and retry logic
- Caching strategies

## Key Takeaways
- Use Fetch API with proper error handling and cancellation
- Implement request/response interceptors for common functionality
- Choose appropriate data fetching libraries based on needs
- Separate server state from client state
- Implement caching strategies for better performance
- Handle loading states and errors gracefully
- Use optimistic updates for better user experience
- Implement retry logic for failed requests
- Monitor and optimize data fetching performance
- Test data fetching logic thoroughly
