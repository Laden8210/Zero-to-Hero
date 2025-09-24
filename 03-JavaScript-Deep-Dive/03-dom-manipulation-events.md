# DOM Manipulation and Events

## Introduction
DOM manipulation and event handling are core skills for creating interactive web applications. This lesson covers advanced DOM selection, event handling, and dynamic content management.

## Advanced DOM Selection and Traversal

### QuerySelector Methods
```javascript
// Advanced CSS selectors
const elements = {
    // Basic selectors
    firstButton: document.querySelector('button'),
    allButtons: document.querySelectorAll('button'),
    
    // Attribute selectors
    requiredInputs: document.querySelectorAll('input[required]'),
    dataElements: document.querySelectorAll('[data-component]'),
    
    // Pseudo-selectors
    firstListItem: document.querySelector('li:first-child'),
    lastListItem: document.querySelector('li:last-child'),
    oddRows: document.querySelectorAll('tr:nth-child(odd)'),
    
    // Complex selectors
    formInputs: document.querySelectorAll('form input:not([type="hidden"])'),
    visibleElements: document.querySelectorAll(':not([hidden])'),
    
    // Descendant and sibling selectors
    nestedLinks: document.querySelectorAll('.card a'),
    adjacentSiblings: document.querySelectorAll('h2 + p'),
    generalSiblings: document.querySelectorAll('h2 ~ p')
};

// Performance considerations
function getElementsEfficiently() {
    // Cache frequently used elements
    const container = document.querySelector('.container');
    const buttons = container.querySelectorAll('button');
    const inputs = container.querySelectorAll('input');
    
    return { container, buttons, inputs };
}

// Live vs static node lists
function demonstrateNodeLists() {
    // Static NodeList (querySelectorAll)
    const staticList = document.querySelectorAll('.item');
    console.log('Static list length:', staticList.length);
    
    // Add new element
    const newItem = document.createElement('div');
    newItem.className = 'item';
    document.body.appendChild(newItem);
    
    // Static list doesn't update
    console.log('Static list length after addition:', staticList.length);
    
    // Live NodeList (getElementsByClassName)
    const liveList = document.getElementsByClassName('item');
    console.log('Live list length:', liveList.length);
    
    // Live list updates automatically
    console.log('Live list length after addition:', liveList.length);
}
```

### Node Relationships and Traversal
```javascript
// Node traversal utilities
class DOMTraverser {
    static getParent(element, selector = null) {
        if (!selector) {
            return element.parentElement;
        }
        
        let parent = element.parentElement;
        while (parent && !parent.matches(selector)) {
            parent = parent.parentElement;
        }
        return parent;
    }
    
    static getChildren(element, selector = null) {
        const children = Array.from(element.children);
        if (!selector) {
            return children;
        }
        return children.filter(child => child.matches(selector));
    }
    
    static getSiblings(element, selector = null) {
        const siblings = Array.from(element.parentElement.children);
        const filtered = siblings.filter(sibling => sibling !== element);
        
        if (!selector) {
            return filtered;
        }
        return filtered.filter(sibling => sibling.matches(selector));
    }
    
    static getNextSibling(element, selector = null) {
        let next = element.nextElementSibling;
        while (next && selector && !next.matches(selector)) {
            next = next.nextElementSibling;
        }
        return next;
    }
    
    static getPreviousSibling(element, selector = null) {
        let prev = element.previousElementSibling;
        while (prev && selector && !prev.matches(selector)) {
            prev = prev.previousElementSibling;
        }
        return prev;
    }
    
    static findAncestor(element, selector) {
        let ancestor = element.parentElement;
        while (ancestor && !ancestor.matches(selector)) {
            ancestor = ancestor.parentElement;
        }
        return ancestor;
    }
}

// Usage examples
function demonstrateTraversal() {
    const button = document.querySelector('button');
    
    // Find parent with specific class
    const card = DOMTraverser.getParent(button, '.card');
    
    // Get all child buttons
    const childButtons = DOMTraverser.getChildren(card, 'button');
    
    // Get sibling elements
    const siblings = DOMTraverser.getSiblings(button);
    
    // Find closest ancestor
    const form = DOMTraverser.findAncestor(button, 'form');
}
```

## Event Handling System

### Event Propagation
```javascript
// Event propagation demonstration
class EventPropagationDemo {
    constructor() {
        this.setupEventListeners();
    }
    
    setupEventListeners() {
        const outer = document.querySelector('.outer');
        const middle = document.querySelector('.middle');
        const inner = document.querySelector('.inner');
        
        // Capturing phase (third parameter: true)
        outer.addEventListener('click', (e) => {
            console.log('Capturing: Outer div');
        }, true);
        
        middle.addEventListener('click', (e) => {
            console.log('Capturing: Middle div');
        }, true);
        
        inner.addEventListener('click', (e) => {
            console.log('Capturing: Inner div');
        }, true);
        
        // Bubbling phase (third parameter: false or omitted)
        outer.addEventListener('click', (e) => {
            console.log('Bubbling: Outer div');
        });
        
        middle.addEventListener('click', (e) => {
            console.log('Bubbling: Middle div');
        });
        
        inner.addEventListener('click', (e) => {
            console.log('Bubbling: Inner div');
        });
    }
}

// Event delegation pattern
class EventDelegation {
    constructor(container) {
        this.container = container;
        this.setupDelegation();
    }
    
    setupDelegation() {
        // Single event listener for multiple elements
        this.container.addEventListener('click', (e) => {
            // Handle button clicks
            if (e.target.matches('button[data-action]')) {
                const action = e.target.dataset.action;
                this.handleAction(action, e.target);
            }
            
            // Handle link clicks
            if (e.target.matches('a[data-navigate]')) {
                const route = e.target.dataset.navigate;
                this.handleNavigation(route, e.target);
            }
            
            // Handle card clicks
            if (e.target.closest('.card')) {
                const card = e.target.closest('.card');
                this.handleCardClick(card);
            }
        });
        
        // Handle form submissions
        this.container.addEventListener('submit', (e) => {
            e.preventDefault();
            const form = e.target;
            this.handleFormSubmit(form);
        });
        
        // Handle input changes
        this.container.addEventListener('input', (e) => {
            if (e.target.matches('input[data-validate]')) {
                this.validateInput(e.target);
            }
        });
    }
    
    handleAction(action, element) {
        console.log(`Action: ${action}`, element);
        // Handle different actions
    }
    
    handleNavigation(route, element) {
        console.log(`Navigate to: ${route}`, element);
        // Handle navigation
    }
    
    handleCardClick(card) {
        console.log('Card clicked:', card);
        // Handle card interaction
    }
    
    handleFormSubmit(form) {
        console.log('Form submitted:', form);
        // Handle form submission
    }
    
    validateInput(input) {
        console.log('Validating input:', input);
        // Handle input validation
    }
}
```

### Custom Events
```javascript
// Custom event creation and dispatching
class CustomEventManager {
    constructor() {
        this.setupCustomEvents();
    }
    
    setupCustomEvents() {
        // Listen for custom events
        document.addEventListener('userLogin', (e) => {
            console.log('User logged in:', e.detail);
            this.updateUI(e.detail);
        });
        
        document.addEventListener('dataLoaded', (e) => {
            console.log('Data loaded:', e.detail);
            this.renderData(e.detail);
        });
        
        document.addEventListener('validationError', (e) => {
            console.log('Validation error:', e.detail);
            this.showError(e.detail);
        });
    }
    
    // Dispatch custom events
    triggerUserLogin(userData) {
        const event = new CustomEvent('userLogin', {
            detail: userData,
            bubbles: true,
            cancelable: true
        });
        document.dispatchEvent(event);
    }
    
    triggerDataLoaded(data) {
        const event = new CustomEvent('dataLoaded', {
            detail: data,
            bubbles: true
        });
        document.dispatchEvent(event);
    }
    
    triggerValidationError(errors) {
        const event = new CustomEvent('validationError', {
            detail: errors,
            bubbles: true
        });
        document.dispatchEvent(event);
    }
    
    updateUI(userData) {
        // Update UI based on user data
    }
    
    renderData(data) {
        // Render data to the page
    }
    
    showError(errors) {
        // Display validation errors
    }
}

// Event bus pattern
class EventBus {
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
        
        this.events[event].forEach(callback => callback(data));
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
const eventBus = new EventBus();

eventBus.on('userAction', (data) => {
    console.log('User action:', data);
});

eventBus.emit('userAction', { type: 'click', target: 'button' });
```

## Dynamic Content Manipulation

### Efficient DOM Operations
```javascript
// DocumentFragment for batch operations
class DOMManipulator {
    static createElements(template) {
        const fragment = document.createDocumentFragment();
        
        template.forEach(item => {
            const element = this.createElement(item);
            fragment.appendChild(element);
        });
        
        return fragment;
    }
    
    static createElement(config) {
        const element = document.createElement(config.tag);
        
        // Set attributes
        if (config.attributes) {
            Object.entries(config.attributes).forEach(([key, value]) => {
                element.setAttribute(key, value);
            });
        }
        
        // Set properties
        if (config.properties) {
            Object.entries(config.properties).forEach(([key, value]) => {
                element[key] = value;
            });
        }
        
        // Add classes
        if (config.classes) {
            element.classList.add(...config.classes);
        }
        
        // Set text content
        if (config.text) {
            element.textContent = config.text;
        }
        
        // Set HTML content
        if (config.html) {
            element.innerHTML = config.html;
        }
        
        // Add event listeners
        if (config.events) {
            Object.entries(config.events).forEach(([event, handler]) => {
                element.addEventListener(event, handler);
            });
        }
        
        // Add children
        if (config.children) {
            config.children.forEach(child => {
                const childElement = this.createElement(child);
                element.appendChild(childElement);
            });
        }
        
        return element;
    }
    
    static batchUpdate(container, updates) {
        const fragment = document.createDocumentFragment();
        
        updates.forEach(update => {
            if (update.type === 'append') {
                fragment.appendChild(this.createElement(update.element));
            } else if (update.type === 'prepend') {
                fragment.insertBefore(this.createElement(update.element), fragment.firstChild);
            }
        });
        
        container.appendChild(fragment);
    }
}

// Usage example
const template = [
    {
        tag: 'div',
        classes: ['card'],
        children: [
            {
                tag: 'h3',
                text: 'Card Title',
                classes: ['card-title']
            },
            {
                tag: 'p',
                text: 'Card content goes here...',
                classes: ['card-content']
            },
            {
                tag: 'button',
                text: 'Click me',
                classes: ['btn', 'btn-primary'],
                events: {
                    click: (e) => console.log('Button clicked!')
                }
            }
        ]
    }
];

const fragment = DOMManipulator.createElements(template);
document.querySelector('.container').appendChild(fragment);
```

### Performance Optimization
```javascript
// Performance optimization techniques
class PerformanceOptimizer {
    static debounce(func, wait) {
        let timeout;
        return function executedFunction(...args) {
            const later = () => {
                clearTimeout(timeout);
                func(...args);
            };
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
        };
    }
    
    static throttle(func, limit) {
        let inThrottle;
        return function(...args) {
            if (!inThrottle) {
                func.apply(this, args);
                inThrottle = true;
                setTimeout(() => inThrottle = false, limit);
            }
        };
    }
    
    static requestAnimationFrame(callback) {
        return window.requestAnimationFrame(callback);
    }
    
    static observeIntersection(element, callback, options = {}) {
        const observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    callback(entry);
                }
            });
        }, options);
        
        observer.observe(element);
        return observer;
    }
    
    static observeMutation(target, callback, options = {}) {
        const observer = new MutationObserver(callback);
        observer.observe(target, options);
        return observer;
    }
}

// Lazy loading implementation
class LazyLoader {
    constructor() {
        this.imageObserver = null;
        this.setupLazyLoading();
    }
    
    setupLazyLoading() {
        const images = document.querySelectorAll('img[data-src]');
        
        this.imageObserver = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src;
                    img.classList.remove('lazy');
                    this.imageObserver.unobserve(img);
                }
            });
        });
        
        images.forEach(img => {
            this.imageObserver.observe(img);
        });
    }
}

// Virtual scrolling for large lists
class VirtualScroller {
    constructor(container, itemHeight, items) {
        this.container = container;
        this.itemHeight = itemHeight;
        this.items = items;
        this.visibleStart = 0;
        this.visibleEnd = 0;
        this.containerHeight = container.clientHeight;
        
        this.setupVirtualScrolling();
    }
    
    setupVirtualScrolling() {
        this.updateVisibleRange();
        this.renderVisibleItems();
        
        this.container.addEventListener('scroll', this.throttle(() => {
            this.updateVisibleRange();
            this.renderVisibleItems();
        }, 16));
    }
    
    updateVisibleRange() {
        const scrollTop = this.container.scrollTop;
        this.visibleStart = Math.floor(scrollTop / this.itemHeight);
        this.visibleEnd = Math.min(
            this.visibleStart + Math.ceil(this.containerHeight / this.itemHeight),
            this.items.length
        );
    }
    
    renderVisibleItems() {
        const fragment = document.createDocumentFragment();
        
        for (let i = this.visibleStart; i < this.visibleEnd; i++) {
            const item = this.createItem(this.items[i], i);
            fragment.appendChild(item);
        }
        
        this.container.innerHTML = '';
        this.container.appendChild(fragment);
    }
    
    createItem(data, index) {
        const item = document.createElement('div');
        item.style.height = `${this.itemHeight}px`;
        item.textContent = data;
        return item;
    }
    
    throttle(func, limit) {
        let inThrottle;
        return function(...args) {
            if (!inThrottle) {
                func.apply(this, args);
                inThrottle = true;
                setTimeout(() => inThrottle = false, limit);
            }
        };
    }
}
```

## Exercise: Interactive DOM Application

Create an interactive DOM application with:
- Advanced DOM selection and traversal
- Event delegation for dynamic content
- Custom events for component communication
- Efficient DOM manipulation with DocumentFragment
- Performance optimization with debouncing and throttling
- Lazy loading for images and content

## Key Takeaways
- Use querySelector methods for precise element selection
- Understand the difference between live and static node lists
- Implement event delegation for better performance
- Create custom events for component communication
- Use DocumentFragment for efficient batch DOM operations
- Apply performance optimization techniques
- Implement lazy loading for better user experience
- Use Intersection Observer for efficient scroll-based operations
- Cache frequently accessed DOM elements
- Minimize DOM manipulation for better performance
