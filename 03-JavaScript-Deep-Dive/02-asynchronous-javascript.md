# Asynchronous JavaScript Mastery

## Introduction
Asynchronous JavaScript is crucial for handling operations that take time, such as API calls, file operations, and user interactions. This lesson covers Promises, async/await, and the event loop to master asynchronous programming.

## Promise Architecture

### Understanding Promises
```javascript
// Promise states: pending, fulfilled, rejected
const promise = new Promise((resolve, reject) => {
    // Asynchronous operation
    setTimeout(() => {
        const success = Math.random() > 0.5;
        if (success) {
            resolve('Operation successful!');
        } else {
            reject(new Error('Operation failed!'));
        }
    }, 1000);
});

// Handling promise results
promise
    .then(result => {
        console.log('Success:', result);
        return result.toUpperCase();
    })
    .then(upperResult => {
        console.log('Uppercase:', upperResult);
    })
    .catch(error => {
        console.error('Error:', error.message);
    })
    .finally(() => {
        console.log('Promise completed (success or failure)');
    });
```

**Code Explanation:**
- `new Promise((resolve, reject) => {...})`: Creates a new Promise with executor function
- `resolve('Operation successful!')`: Resolves the promise with a success value
- `reject(new Error('Operation failed!'))`: Rejects the promise with an error
- `.then(result => {...})`: Handles successful promise resolution
- `return result.toUpperCase()`: Returns a new value for the next `.then()`
- `.catch(error => {...})`: Handles promise rejection and errors
- `.finally(() => {...})`: Executes regardless of success or failure
- `setTimeout(() => {...}, 1000)`: Simulates asynchronous operation with 1-second delay

**Benefits:**
- Promises provide cleaner error handling than callbacks
- Chainable `.then()` methods enable sequential operations
- `.catch()` centralizes error handling
- `.finally()` ensures cleanup code always runs
- Eliminates callback hell and improves code readability

### Promise Creation Patterns
```javascript
// Wrapping callback-based functions
function readFileAsync(filename) {
    return new Promise((resolve, reject) => {
        // Simulate file reading
        setTimeout(() => {
            if (filename === 'error.txt') {
                reject(new Error('File not found'));
            } else {
                resolve(`Content of ${filename}`);
            }
        }, 1000);
    });
}

// Using the promise
readFileAsync('data.txt')
    .then(content => console.log(content))
    .catch(error => console.error(error.message));

// Promise.resolve and Promise.reject
const resolvedPromise = Promise.resolve('Immediate success');
const rejectedPromise = Promise.reject(new Error('Immediate failure'));

// Converting values to promises
function processData(data) {
    return Promise.resolve(data)
        .then(value => value.toUpperCase())
        .then(value => value.split(''))
        .then(chars => chars.join('-'));
}

processData('hello').then(result => console.log(result)); // "H-E-L-L-O"
```

### Promise Chaining and Composition
```javascript
// Sequential promise chaining
function fetchUserData(userId) {
    return fetch(`/api/users/${userId}`)
        .then(response => {
            if (!response.ok) {
                throw new Error('User not found');
            }
            return response.json();
        })
        .then(user => {
            console.log('User data:', user);
            return fetch(`/api/users/${userId}/posts`);
        })
        .then(response => response.json())
        .then(posts => {
            console.log('User posts:', posts);
            return { user, posts };
        })
        .catch(error => {
            console.error('Error fetching user data:', error);
            throw error;
        });
}

// Promise.all - wait for all promises
function fetchMultipleUsers(userIds) {
    const userPromises = userIds.map(id => 
        fetch(`/api/users/${id}`).then(response => response.json())
    );
    
    return Promise.all(userPromises)
        .then(users => {
            console.log('All users fetched:', users);
            return users;
        })
        .catch(error => {
            console.error('Error fetching users:', error);
            throw error;
        });
}

// Promise.race - first promise to resolve/reject
function fetchWithTimeout(url, timeout = 5000) {
    const fetchPromise = fetch(url).then(response => response.json());
    const timeoutPromise = new Promise((_, reject) => 
        setTimeout(() => reject(new Error('Request timeout')), timeout)
    );
    
    return Promise.race([fetchPromise, timeoutPromise]);
}

// Promise.allSettled - wait for all promises regardless of outcome
function fetchUserDataSafely(userIds) {
    const userPromises = userIds.map(id => 
        fetch(`/api/users/${id}`)
            .then(response => response.json())
            .catch(error => ({ error: error.message, id }))
    );
    
    return Promise.allSettled(userPromises)
        .then(results => {
            const successful = results
                .filter(result => result.status === 'fulfilled')
                .map(result => result.value);
            
            const failed = results
                .filter(result => result.status === 'rejected')
                .map(result => result.reason);
            
            return { successful, failed };
        });
}
```

## Async/Await Patterns

### Basic Async/Await
```javascript
// Converting promise chains to async/await
async function fetchUserDataAsync(userId) {
    try {
        const userResponse = await fetch(`/api/users/${userId}`);
        if (!userResponse.ok) {
            throw new Error('User not found');
        }
        const user = await userResponse.json();
        
        const postsResponse = await fetch(`/api/users/${userId}/posts`);
        const posts = await postsResponse.json();
        
        return { user, posts };
    } catch (error) {
        console.error('Error fetching user data:', error);
        throw error;
    }
}

// Using async/await
async function displayUserData(userId) {
    try {
        const data = await fetchUserDataAsync(userId);
        console.log('User:', data.user);
        console.log('Posts:', data.posts);
    } catch (error) {
        console.error('Failed to load user data:', error.message);
    }
}
```

### Parallel Execution with Async/Await
```javascript
// Sequential execution (slower)
async function fetchSequential(userIds) {
    const users = [];
    for (const id of userIds) {
        const response = await fetch(`/api/users/${id}`);
        const user = await response.json();
        users.push(user);
    }
    return users;
}

// Parallel execution (faster)
async function fetchParallel(userIds) {
    const userPromises = userIds.map(async (id) => {
        const response = await fetch(`/api/users/${id}`);
        return response.json();
    });
    
    return Promise.all(userPromises);
}

// Mixed approach - parallel with error handling
async function fetchUsersWithErrorHandling(userIds) {
    const results = await Promise.allSettled(
        userIds.map(async (id) => {
            const response = await fetch(`/api/users/${id}`);
            if (!response.ok) {
                throw new Error(`Failed to fetch user ${id}`);
            }
            return response.json();
        })
    );
    
    const successful = results
        .filter(result => result.status === 'fulfilled')
        .map(result => result.value);
    
    const errors = results
        .filter(result => result.status === 'rejected')
        .map(result => result.reason);
    
    return { successful, errors };
}
```

### Advanced Async Patterns
```javascript
// Async generator for streaming data
async function* fetchUserPages(pageSize = 10) {
    let page = 1;
    let hasMore = true;
    
    while (hasMore) {
        const response = await fetch(`/api/users?page=${page}&size=${pageSize}`);
        const data = await response.json();
        
        yield data.users;
        
        hasMore = data.hasMore;
        page++;
    }
}

// Using async generator
async function processAllUsers() {
    for await (const users of fetchUserPages()) {
        console.log('Processing batch:', users);
        // Process each batch of users
    }
}

// Retry mechanism with exponential backoff
async function fetchWithRetry(url, maxRetries = 3) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
            const response = await fetch(url);
            if (response.ok) {
                return response.json();
            }
            throw new Error(`HTTP ${response.status}`);
        } catch (error) {
            if (attempt === maxRetries) {
                throw error;
            }
            
            const delay = Math.pow(2, attempt) * 1000; // Exponential backoff
            console.log(`Attempt ${attempt} failed, retrying in ${delay}ms`);
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}

// Cancellation with AbortController
async function fetchWithCancellation(url, signal) {
    const response = await fetch(url, { signal });
    return response.json();
}

// Usage with cancellation
const controller = new AbortController();
const timeoutId = setTimeout(() => controller.abort(), 5000);

try {
    const data = await fetchWithCancellation('/api/data', controller.signal);
    console.log('Data received:', data);
} catch (error) {
    if (error.name === 'AbortError') {
        console.log('Request was cancelled');
    } else {
        console.error('Request failed:', error);
    }
} finally {
    clearTimeout(timeoutId);
}
```

## Event Loop and Concurrency Model

### Understanding the Event Loop
```javascript
// Microtasks vs Macrotasks
console.log('1. Synchronous code');

setTimeout(() => console.log('2. Macrotask (setTimeout)'), 0);

Promise.resolve().then(() => console.log('3. Microtask (Promise)'));

queueMicrotask(() => console.log('4. Microtask (queueMicrotask)'));

setTimeout(() => console.log('5. Macrotask (setTimeout)'), 0);

console.log('6. Synchronous code');

// Output order:
// 1. Synchronous code
// 6. Synchronous code
// 3. Microtask (Promise)
// 4. Microtask (queueMicrotask)
// 2. Macrotask (setTimeout)
// 5. Macrotask (setTimeout)
```

### Non-blocking I/O Operations
```javascript
// Blocking operation (bad)
function blockingOperation() {
    const start = Date.now();
    while (Date.now() - start < 2000) {
        // Blocking for 2 seconds
    }
    console.log('Blocking operation completed');
}

// Non-blocking operation (good)
function nonBlockingOperation() {
    return new Promise(resolve => {
        setTimeout(() => {
            console.log('Non-blocking operation completed');
            resolve();
        }, 2000);
    });
}

// Demonstrating the difference
async function demonstrateBlocking() {
    console.log('Starting blocking operation');
    blockingOperation(); // Blocks the entire thread
    console.log('This won\'t execute until blocking operation completes');
}

async function demonstrateNonBlocking() {
    console.log('Starting non-blocking operation');
    await nonBlockingOperation(); // Doesn't block the thread
    console.log('This executes after the timeout');
}

// Performance implications
async function performanceComparison() {
    console.time('Blocking approach');
    blockingOperation();
    console.timeEnd('Blocking approach');
    
    console.time('Non-blocking approach');
    await nonBlockingOperation();
    console.timeEnd('Non-blocking approach');
}
```

### Advanced Concurrency Patterns
```javascript
// Semaphore for limiting concurrent operations
class Semaphore {
    constructor(maxConcurrent) {
        this.maxConcurrent = maxConcurrent;
        this.current = 0;
        this.queue = [];
    }
    
    async acquire() {
        return new Promise(resolve => {
            if (this.current < this.maxConcurrent) {
                this.current++;
                resolve();
            } else {
                this.queue.push(resolve);
            }
        });
    }
    
    release() {
        this.current--;
        if (this.queue.length > 0) {
            const resolve = this.queue.shift();
            this.current++;
            resolve();
        }
    }
}

// Using semaphore to limit concurrent API calls
const apiSemaphore = new Semaphore(3);

async function limitedApiCall(url) {
    await apiSemaphore.acquire();
    try {
        const response = await fetch(url);
        return response.json();
    } finally {
        apiSemaphore.release();
    }
}

// Debouncing and throttling
function debounce(func, delay) {
    let timeoutId;
    return function (...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

function throttle(func, limit) {
    let inThrottle;
    return function (...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

// Usage examples
const debouncedSearch = debounce(async (query) => {
    if (query.length > 2) {
        const results = await fetch(`/api/search?q=${query}`);
        const data = await results.json();
        console.log('Search results:', data);
    }
}, 300);

const throttledScroll = throttle(() => {
    console.log('Scroll event processed');
}, 100);
```

## Exercise: Async Data Management System

Create an asynchronous data management system with:
- Promise-based API calls with error handling
- Async/await for clean asynchronous code
- Parallel data fetching with Promise.all
- Retry mechanism for failed requests
- Debounced search functionality
- Loading states and error boundaries

## Key Takeaways
- Promises provide a clean way to handle asynchronous operations
- Async/await makes asynchronous code more readable
- Use Promise.all for parallel execution, Promise.allSettled for error handling
- The event loop processes microtasks before macrotasks
- Non-blocking operations prevent UI freezing
- Implement proper error handling and retry mechanisms
- Use debouncing and throttling for performance optimization
