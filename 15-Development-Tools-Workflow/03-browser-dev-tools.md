# Browser Dev Tools Mastery

## Introduction
Browser developer tools are essential for debugging, profiling, and optimizing web applications. This lesson covers Chrome DevTools, Firefox DevTools, and advanced debugging techniques.

## Chrome DevTools

### Elements Panel
```html
<!-- HTML Structure for DevTools Practice -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DevTools Practice</title>
    <style>
        .container {
            display: flex;
            flex-direction: column;
            gap: 20px;
            padding: 20px;
        }
        
        .card {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease;
        }
        
        .card:hover {
            transform: translateY(-5px);
        }
        
        .button {
            background: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        
        .button:hover {
            background: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="card">
            <h2>Card Title</h2>
            <p>This is a sample card for DevTools practice.</p>
            <button class="button">Click Me</button>
        </div>
        
        <div class="card">
            <h2>Another Card</h2>
            <p>Another sample card with different content.</p>
            <button class="button">Another Button</button>
        </div>
    </div>
    
    <script>
        // JavaScript for DevTools Practice
        console.log('Page loaded');
        
        document.addEventListener('DOMContentLoaded', function() {
            const buttons = document.querySelectorAll('.button');
            
            buttons.forEach(button => {
                button.addEventListener('click', function() {
                    console.log('Button clicked:', this.textContent);
                    this.style.background = '#ff6b6b';
                });
            });
        });
    </script>
</body>
</html>
```

### Console Panel
```javascript
// Console Commands and Techniques
console.log('Basic logging');
console.info('Informational message');
console.warn('Warning message');
console.error('Error message');
console.debug('Debug message');

// Console Grouping
console.group('User Actions');
console.log('User clicked button');
console.log('User navigated to page');
console.groupEnd();

// Console Timing
console.time('API Call');
fetch('/api/data')
    .then(response => response.json())
    .then(data => {
        console.timeEnd('API Call');
        console.log('Data received:', data);
    });

// Console Table
const users = [
    { name: 'John', age: 30, city: 'New York' },
    { name: 'Jane', age: 25, city: 'London' },
    { name: 'Bob', age: 35, city: 'Paris' }
];
console.table(users);

// Console Assertions
console.assert(users.length > 0, 'Users array should not be empty');

// Console Count
console.count('Button Clicks');
console.count('Button Clicks');
console.count('Button Clicks');

// Console Trace
function firstFunction() {
    secondFunction();
}

function secondFunction() {
    thirdFunction();
}

function thirdFunction() {
    console.trace('Function call trace');
}

firstFunction();

// Console Clear
console.clear();

// Console Dir
const element = document.querySelector('.card');
console.dir(element);

// Console Dirxml
console.dirxml(document.body);
```

### Sources Panel
```javascript
// Debugging Techniques
function calculateTotal(items) {
    let total = 0;
    
    // Set breakpoint here
    debugger;
    
    for (let item of items) {
        total += item.price * item.quantity;
    }
    
    return total;
}

// Conditional Breakpoints
function processUser(user) {
    if (user.age > 18) {
        // Conditional breakpoint: user.name === 'John'
        console.log('Processing adult user:', user.name);
    }
}

// Logpoints
function fetchData() {
    // Logpoint: "Fetching data for user: " + user.id
    return fetch(`/api/users/${user.id}`);
}

// Watch Expressions
const user = { name: 'John', age: 30 };
// Watch: user.name
// Watch: user.age > 18
// Watch: Object.keys(user)

// Call Stack
function level1() {
    level2();
}

function level2() {
    level3();
}

function level3() {
    console.log('Level 3 reached');
    // Check call stack here
}

level1();
```

### Network Panel
```javascript
// Network Monitoring
fetch('/api/users')
    .then(response => {
        console.log('Response status:', response.status);
        console.log('Response headers:', response.headers);
        return response.json();
    })
    .then(data => {
        console.log('Data received:', data);
    })
    .catch(error => {
        console.error('Network error:', error);
    });

// Request Interception
const originalFetch = window.fetch;
window.fetch = function(...args) {
    console.log('Fetch request:', args[0]);
    return originalFetch.apply(this, args);
};

// Response Monitoring
const originalXHR = XMLHttpRequest.prototype.open;
XMLHttpRequest.prototype.open = function(method, url) {
    console.log('XHR request:', method, url);
    return originalXHR.apply(this, arguments);
};
```

### Performance Panel
```javascript
// Performance Monitoring
function measurePerformance() {
    const start = performance.now();
    
    // Simulate heavy computation
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
        result += Math.random();
    }
    
    const end = performance.now();
    console.log(`Computation took ${end - start} milliseconds`);
    
    return result;
}

// Memory Usage
function checkMemoryUsage() {
    if (performance.memory) {
        console.log('Memory usage:', {
            used: Math.round(performance.memory.usedJSHeapSize / 1048576) + ' MB',
            total: Math.round(performance.memory.totalJSHeapSize / 1048576) + ' MB',
            limit: Math.round(performance.memory.jsHeapSizeLimit / 1048576) + ' MB'
        });
    }
}

// Performance Observer
const observer = new PerformanceObserver((list) => {
    for (const entry of list.getEntries()) {
        console.log('Performance entry:', entry);
    }
});

observer.observe({ entryTypes: ['measure', 'navigation', 'resource'] });
```

### Application Panel
```javascript
// Local Storage
localStorage.setItem('user', JSON.stringify({ name: 'John', age: 30 }));
const user = JSON.parse(localStorage.getItem('user'));
console.log('User from localStorage:', user);

// Session Storage
sessionStorage.setItem('sessionId', 'abc123');
const sessionId = sessionStorage.getItem('sessionId');
console.log('Session ID:', sessionId);

// IndexedDB
const request = indexedDB.open('MyDatabase', 1);

request.onupgradeneeded = function(event) {
    const db = event.target.result;
    const objectStore = db.createObjectStore('users', { keyPath: 'id' });
    objectStore.createIndex('name', 'name', { unique: false });
};

request.onsuccess = function(event) {
    const db = event.target.result;
    const transaction = db.transaction(['users'], 'readwrite');
    const objectStore = transaction.objectStore('users');
    
    const user = { id: 1, name: 'John', age: 30 };
    objectStore.add(user);
};

// Service Workers
if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/sw.js')
        .then(registration => {
            console.log('Service Worker registered:', registration);
        })
        .catch(error => {
            console.error('Service Worker registration failed:', error);
        });
}
```

## Firefox DevTools

### Responsive Design Mode
```css
/* Responsive Design CSS */
.container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 20px;
    padding: 20px;
}

.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
}

@media (max-width: 768px) {
    .container {
        grid-template-columns: 1fr;
        padding: 10px;
    }
    
    .card {
        padding: 15px;
    }
}

@media (max-width: 480px) {
    .container {
        padding: 5px;
    }
    
    .card {
        padding: 10px;
    }
}
```

### Accessibility Inspector
```html
<!-- Accessible HTML Structure -->
<main role="main">
    <h1>Accessible Page</h1>
    
    <nav aria-label="Main navigation">
        <ul>
            <li><a href="#home" aria-current="page">Home</a></li>
            <li><a href="#about">About</a></li>
            <li><a href="#contact">Contact</a></li>
        </ul>
    </nav>
    
    <section aria-labelledby="content-heading">
        <h2 id="content-heading">Main Content</h2>
        <p>This is the main content of the page.</p>
        
        <form aria-label="Contact form">
            <label for="name">Name:</label>
            <input type="text" id="name" name="name" required aria-describedby="name-help">
            <div id="name-help">Enter your full name</div>
            
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
            
            <button type="submit" aria-describedby="submit-help">Submit</button>
            <div id="submit-help">Click to submit the form</div>
        </form>
    </section>
    
    <aside aria-label="Additional information">
        <h3>Additional Info</h3>
        <p>This is additional information.</p>
    </aside>
</main>
```

## Advanced Debugging Techniques

### Error Handling
```javascript
// Global Error Handling
window.addEventListener('error', function(event) {
    console.error('Global error:', event.error);
    console.error('Error message:', event.message);
    console.error('Error source:', event.filename);
    console.error('Error line:', event.lineno);
    console.error('Error column:', event.colno);
});

// Unhandled Promise Rejections
window.addEventListener('unhandledrejection', function(event) {
    console.error('Unhandled promise rejection:', event.reason);
    event.preventDefault();
});

// Try-Catch with Stack Trace
function riskyFunction() {
    try {
        throw new Error('Something went wrong');
    } catch (error) {
        console.error('Error caught:', error);
        console.error('Stack trace:', error.stack);
    }
}

// Custom Error Classes
class CustomError extends Error {
    constructor(message, code) {
        super(message);
        this.name = 'CustomError';
        this.code = code;
    }
}

function validateInput(input) {
    if (!input) {
        throw new CustomError('Input is required', 'VALIDATION_ERROR');
    }
}
```

### Performance Profiling
```javascript
// Performance Profiling
function profileFunction(func, name) {
    return function(...args) {
        const start = performance.now();
        const result = func.apply(this, args);
        const end = performance.now();
        
        console.log(`${name} took ${end - start} milliseconds`);
        return result;
    };
}

// Memory Leak Detection
function detectMemoryLeaks() {
    const initialMemory = performance.memory ? performance.memory.usedJSHeapSize : 0;
    
    // Perform operations that might cause memory leaks
    for (let i = 0; i < 1000; i++) {
        const element = document.createElement('div');
        element.innerHTML = 'Memory leak test';
        document.body.appendChild(element);
        // Don't remove the element to simulate memory leak
    }
    
    setTimeout(() => {
        const finalMemory = performance.memory ? performance.memory.usedJSHeapSize : 0;
        const memoryIncrease = finalMemory - initialMemory;
        
        if (memoryIncrease > 1024 * 1024) { // 1MB threshold
            console.warn('Potential memory leak detected:', memoryIncrease, 'bytes');
        }
    }, 1000);
}
```

## Exercise: DevTools Mastery

Practice using browser developer tools with:
- Elements inspection and modification
- Console debugging techniques
- Sources panel debugging
- Network monitoring
- Performance profiling
- Application panel usage
- Responsive design testing
- Accessibility inspection

## Key Takeaways
- Master Chrome DevTools for debugging
- Use Firefox DevTools for responsive design
- Learn advanced debugging techniques
- Monitor network requests and responses
- Profile performance and memory usage
- Use accessibility tools for inclusive design
- Debug JavaScript errors effectively
- Use breakpoints and watch expressions
- Monitor application storage
- Use performance observers
- Handle errors gracefully
- Detect memory leaks
- Use responsive design mode
- Inspect accessibility features
- Use console commands effectively
