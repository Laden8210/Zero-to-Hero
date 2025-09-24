# Event Handling Patterns

## Introduction
Event handling is a fundamental aspect of interactive web applications. This lesson covers event delegation, custom events, event bubbling, and modern event handling patterns for scalable applications.

## Event Delegation

### Basic Event Delegation
```javascript
// Event delegation for dynamic content
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
            
            // Handle form submissions
            if (e.target.matches('form')) {
                e.preventDefault();
                this.handleFormSubmit(e.target);
            }
        });
        
        // Handle input changes
        this.container.addEventListener('input', (e) => {
            if (e.target.matches('input[data-validate]')) {
                this.validateInput(e.target);
            }
        });
        
        // Handle keyboard events
        this.container.addEventListener('keydown', (e) => {
            if (e.target.matches('input[data-autocomplete]')) {
                this.handleAutocomplete(e.target, e);
            }
        });
    }
    
    handleAction(action, element) {
        console.log(`Action: ${action}`, element);
        
        switch (action) {
            case 'delete':
                this.deleteItem(element);
                break;
            case 'edit':
                this.editItem(element);
                break;
            case 'save':
                this.saveItem(element);
                break;
            default:
                console.warn(`Unknown action: ${action}`);
        }
    }
    
    handleNavigation(route, element) {
        console.log(`Navigate to: ${route}`, element);
        // Handle navigation logic
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
    
    handleAutocomplete(input, event) {
        console.log('Autocomplete:', input, event);
        // Handle autocomplete logic
    }
    
    deleteItem(element) {
        const item = element.closest('.item');
        if (item) {
            item.remove();
        }
    }
    
    editItem(element) {
        const item = element.closest('.item');
        if (item) {
            // Enable edit mode
            item.classList.add('editing');
        }
    }
    
    saveItem(element) {
        const item = element.closest('.item');
        if (item) {
            // Save changes
            item.classList.remove('editing');
        }
    }
}

// Usage
const container = document.querySelector('.app-container');
const eventDelegation = new EventDelegation(container);
```

**Code Explanation:**
- `class EventDelegation`: Creates a class to manage event delegation
- `constructor(container)`: Initializes with a container element
- `addEventListener('click', (e) => {...})`: Attaches single click listener to container
- `e.target`: The actual element that was clicked
- `e.target.matches('button[data-action]')`: Checks if clicked element matches selector
- `e.target.dataset.action`: Accesses data-action attribute value
- `e.target.closest('.card')`: Finds nearest ancestor matching selector
- `e.preventDefault()`: Prevents default form submission behavior
- `addEventListener('input', (e) => {...})`: Handles input changes
- `addEventListener('keydown', (e) => {...})`: Handles keyboard events
- `switch (action)`: Routes different actions to appropriate handlers
- `element.closest('.item')`: Finds parent item element
- `item.remove()`: Removes element from DOM
- `item.classList.add('editing')`: Adds CSS class to element
- `new EventDelegation(container)`: Creates instance with container element

**Benefits:**
- Single event listener handles multiple elements efficiently
- Works with dynamically added content
- Reduces memory usage compared to individual listeners
- Centralized event handling logic
- Easy to maintain and debug

### Advanced Event Delegation
```javascript
// Advanced event delegation with multiple event types
class AdvancedEventDelegation {
    constructor(container) {
        this.container = container;
        this.eventHandlers = new Map();
        this.setupDelegation();
    }
    
    setupDelegation() {
        // Mouse events
        this.delegate('click', this.handleClick.bind(this));
        this.delegate('dblclick', this.handleDoubleClick.bind(this));
        this.delegate('mouseover', this.handleMouseOver.bind(this));
        this.delegate('mouseout', this.handleMouseOut.bind(this));
        
        // Touch events
        this.delegate('touchstart', this.handleTouchStart.bind(this));
        this.delegate('touchend', this.handleTouchEnd.bind(this));
        
        // Keyboard events
        this.delegate('keydown', this.handleKeyDown.bind(this));
        this.delegate('keyup', this.handleKeyUp.bind(this));
        
        // Form events
        this.delegate('submit', this.handleSubmit.bind(this));
        this.delegate('change', this.handleChange.bind(this));
        this.delegate('input', this.handleInput.bind(this));
        
        // Custom events
        this.delegate('custom:action', this.handleCustomAction.bind(this));
    }
    
    delegate(eventType, handler) {
        this.container.addEventListener(eventType, handler);
        this.eventHandlers.set(eventType, handler);
    }
    
    handleClick(e) {
        const target = e.target;
        
        // Button actions
        if (target.matches('button[data-action]')) {
            const action = target.dataset.action;
            this.triggerAction(action, target, e);
        }
        
        // Toggle elements
        if (target.matches('[data-toggle]')) {
            const toggleTarget = target.dataset.toggle;
            this.toggleElement(toggleTarget, target);
        }
        
        // Modal triggers
        if (target.matches('[data-modal]')) {
            const modalId = target.dataset.modal;
            this.openModal(modalId);
        }
        
        // Dropdown toggles
        if (target.matches('[data-dropdown]')) {
            const dropdownId = target.dataset.dropdown;
            this.toggleDropdown(dropdownId);
        }
    }
    
    handleDoubleClick(e) {
        const target = e.target;
        
        if (target.matches('.editable')) {
            this.enableEditMode(target);
        }
    }
    
    handleMouseOver(e) {
        const target = e.target;
        
        if (target.matches('.tooltip-trigger')) {
            this.showTooltip(target);
        }
    }
    
    handleMouseOut(e) {
        const target = e.target;
        
        if (target.matches('.tooltip-trigger')) {
            this.hideTooltip(target);
        }
    }
    
    handleTouchStart(e) {
        const target = e.target;
        
        if (target.matches('.touchable')) {
            target.classList.add('touching');
        }
    }
    
    handleTouchEnd(e) {
        const target = e.target;
        
        if (target.matches('.touchable')) {
            target.classList.remove('touching');
        }
    }
    
    handleKeyDown(e) {
        const target = e.target;
        
        // Escape key handling
        if (e.key === 'Escape') {
            this.handleEscapeKey(target);
        }
        
        // Enter key handling
        if (e.key === 'Enter') {
            this.handleEnterKey(target);
        }
        
        // Arrow key handling
        if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(e.key)) {
            this.handleArrowKeys(target, e);
        }
    }
    
    handleKeyUp(e) {
        const target = e.target;
        
        if (target.matches('input[data-search]')) {
            this.handleSearchInput(target);
        }
    }
    
    handleSubmit(e) {
        e.preventDefault();
        const form = e.target;
        
        if (form.matches('form[data-ajax]')) {
            this.handleAjaxSubmit(form);
        } else {
            this.handleRegularSubmit(form);
        }
    }
    
    handleChange(e) {
        const target = e.target;
        
        if (target.matches('select[data-filter]')) {
            this.handleFilterChange(target);
        }
    }
    
    handleInput(e) {
        const target = e.target;
        
        if (target.matches('input[data-autocomplete]')) {
            this.handleAutocomplete(target);
        }
    }
    
    handleCustomAction(e) {
        const action = e.detail.action;
        const data = e.detail.data;
        
        console.log('Custom action:', action, data);
    }
    
    // Action handlers
    triggerAction(action, element, event) {
        const actions = {
            delete: () => this.deleteItem(element),
            edit: () => this.editItem(element),
            save: () => this.saveItem(element),
            cancel: () => this.cancelAction(element),
            confirm: () => this.confirmAction(element)
        };
        
        if (actions[action]) {
            actions[action]();
        }
    }
    
    toggleElement(selector, trigger) {
        const element = document.querySelector(selector);
        if (element) {
            element.classList.toggle('active');
        }
    }
    
    openModal(modalId) {
        const modal = document.getElementById(modalId);
        if (modal) {
            modal.classList.add('active');
            document.body.classList.add('modal-open');
        }
    }
    
    toggleDropdown(dropdownId) {
        const dropdown = document.getElementById(dropdownId);
        if (dropdown) {
            dropdown.classList.toggle('open');
        }
    }
    
    enableEditMode(element) {
        element.classList.add('editing');
        element.focus();
    }
    
    showTooltip(element) {
        const tooltip = element.querySelector('.tooltip');
        if (tooltip) {
            tooltip.classList.add('visible');
        }
    }
    
    hideTooltip(element) {
        const tooltip = element.querySelector('.tooltip');
        if (tooltip) {
            tooltip.classList.remove('visible');
        }
    }
    
    handleEscapeKey(target) {
        // Close modals
        const openModal = document.querySelector('.modal.active');
        if (openModal) {
            openModal.classList.remove('active');
            document.body.classList.remove('modal-open');
        }
        
        // Close dropdowns
        const openDropdown = document.querySelector('.dropdown.open');
        if (openDropdown) {
            openDropdown.classList.remove('open');
        }
        
        // Cancel edit mode
        const editingElement = document.querySelector('.editing');
        if (editingElement) {
            editingElement.classList.remove('editing');
        }
    }
    
    handleEnterKey(target) {
        if (target.matches('input[data-save-on-enter]')) {
            this.saveInput(target);
        }
    }
    
    handleArrowKeys(target, event) {
        if (target.matches('.navigable')) {
            this.handleNavigation(target, event);
        }
    }
    
    handleSearchInput(input) {
        const query = input.value;
        if (query.length >= 2) {
            this.performSearch(query);
        }
    }
    
    handleAjaxSubmit(form) {
        const formData = new FormData(form);
        const url = form.action;
        
        fetch(url, {
            method: 'POST',
            body: formData
        })
        .then(response => response.json())
        .then(data => {
            this.handleAjaxResponse(data, form);
        })
        .catch(error => {
            this.handleAjaxError(error, form);
        });
    }
    
    handleRegularSubmit(form) {
        // Handle regular form submission
        console.log('Regular form submission:', form);
    }
    
    handleFilterChange(select) {
        const filter = select.value;
        this.applyFilter(filter);
    }
    
    handleAutocomplete(input) {
        const query = input.value;
        if (query.length >= 2) {
            this.showAutocompleteSuggestions(query, input);
        }
    }
    
    // Utility methods
    deleteItem(element) {
        const item = element.closest('.item');
        if (item) {
            item.remove();
        }
    }
    
    editItem(element) {
        const item = element.closest('.item');
        if (item) {
            item.classList.add('editing');
        }
    }
    
    saveItem(element) {
        const item = element.closest('.item');
        if (item) {
            item.classList.remove('editing');
        }
    }
    
    cancelAction(element) {
        const item = element.closest('.item');
        if (item) {
            item.classList.remove('editing');
        }
    }
    
    confirmAction(element) {
        const item = element.closest('.item');
        if (item) {
            // Confirm action logic
            console.log('Action confirmed:', item);
        }
    }
    
    saveInput(input) {
        // Save input logic
        console.log('Input saved:', input.value);
    }
    
    handleNavigation(target, event) {
        // Navigation logic
        console.log('Navigation:', event.key, target);
    }
    
    performSearch(query) {
        // Search logic
        console.log('Searching for:', query);
    }
    
    handleAjaxResponse(data, form) {
        // Handle AJAX response
        console.log('AJAX response:', data);
    }
    
    handleAjaxError(error, form) {
        // Handle AJAX error
        console.error('AJAX error:', error);
    }
    
    applyFilter(filter) {
        // Apply filter logic
        console.log('Applying filter:', filter);
    }
    
    showAutocompleteSuggestions(query, input) {
        // Show autocomplete suggestions
        console.log('Autocomplete for:', query);
    }
}

// Usage
const container = document.querySelector('.app-container');
const advancedDelegation = new AdvancedEventDelegation(container);
```

## Custom Events

### Creating and Dispatching Custom Events
```javascript
// Custom event system
class CustomEventSystem {
    constructor() {
        this.eventBus = new EventTarget();
        this.setupCustomEvents();
    }
    
    setupCustomEvents() {
        // Listen for custom events
        this.eventBus.addEventListener('userLogin', (e) => {
            console.log('User logged in:', e.detail);
            this.handleUserLogin(e.detail);
        });
        
        this.eventBus.addEventListener('dataLoaded', (e) => {
            console.log('Data loaded:', e.detail);
            this.handleDataLoaded(e.detail);
        });
        
        this.eventBus.addEventListener('validationError', (e) => {
            console.log('Validation error:', e.detail);
            this.handleValidationError(e.detail);
        });
        
        this.eventBus.addEventListener('componentUpdate', (e) => {
            console.log('Component updated:', e.detail);
            this.handleComponentUpdate(e.detail);
        });
    }
    
    // Event dispatching methods
    dispatchUserLogin(userData) {
        const event = new CustomEvent('userLogin', {
            detail: userData,
            bubbles: true,
            cancelable: true
        });
        this.eventBus.dispatchEvent(event);
    }
    
    dispatchDataLoaded(data) {
        const event = new CustomEvent('dataLoaded', {
            detail: data,
            bubbles: true
        });
        this.eventBus.dispatchEvent(event);
    }
    
    dispatchValidationError(errors) {
        const event = new CustomEvent('validationError', {
            detail: errors,
            bubbles: true
        });
        this.eventBus.dispatchEvent(event);
    }
    
    dispatchComponentUpdate(componentData) {
        const event = new CustomEvent('componentUpdate', {
            detail: componentData,
            bubbles: true
        });
        this.eventBus.dispatchEvent(event);
    }
    
    // Event handlers
    handleUserLogin(userData) {
        // Update UI based on user data
        this.updateUserInterface(userData);
        this.loadUserData(userData.id);
    }
    
    handleDataLoaded(data) {
        // Render data to the page
        this.renderData(data);
        this.updateLoadingState(false);
    }
    
    handleValidationError(errors) {
        // Display validation errors
        this.showValidationErrors(errors);
    }
    
    handleComponentUpdate(componentData) {
        // Update component state
        this.updateComponentState(componentData);
    }
    
    // Utility methods
    updateUserInterface(userData) {
        console.log('Updating UI for user:', userData);
    }
    
    loadUserData(userId) {
        console.log('Loading data for user:', userId);
    }
    
    renderData(data) {
        console.log('Rendering data:', data);
    }
    
    updateLoadingState(isLoading) {
        console.log('Loading state:', isLoading);
    }
    
    showValidationErrors(errors) {
        console.log('Showing validation errors:', errors);
    }
    
    updateComponentState(componentData) {
        console.log('Updating component state:', componentData);
    }
}

// Usage
const eventSystem = new CustomEventSystem();

// Dispatch events
eventSystem.dispatchUserLogin({ id: 1, name: 'John Doe', email: 'john@example.com' });
eventSystem.dispatchDataLoaded([{ id: 1, title: 'Item 1' }, { id: 2, title: 'Item 2' }]);
eventSystem.dispatchValidationError({ email: 'Invalid email format' });
```

### Event Bus Pattern
```javascript
// Event bus implementation
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
    
    // Async event handling
    async emitAsync(event, data) {
        if (!this.events[event]) return;
        
        const promises = this.events[event].map(callback => {
            try {
                return Promise.resolve(callback(data));
            } catch (error) {
                console.error(`Error in async event handler for ${event}:`, error);
                return Promise.reject(error);
            }
        });
        
        return Promise.all(promises);
    }
    
    // Event priority
    onWithPriority(event, callback, priority = 0) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        
        this.events[event].push({ callback, priority });
        this.events[event].sort((a, b) => b.priority - a.priority);
    }
    
    emitWithPriority(event, data) {
        if (!this.events[event]) return;
        
        this.events[event].forEach(({ callback }) => {
            try {
                callback(data);
            } catch (error) {
                console.error(`Error in priority event handler for ${event}:`, error);
            }
        });
    }
}

// Usage
const eventBus = new EventBus();

// Subscribe to events
eventBus.on('userAction', (data) => {
    console.log('User action:', data);
});

eventBus.on('dataChange', (data) => {
    console.log('Data changed:', data);
});

// Emit events
eventBus.emit('userAction', { type: 'click', target: 'button' });
eventBus.emit('dataChange', { id: 1, value: 'new value' });

// One-time event
eventBus.once('initialization', (data) => {
    console.log('Initialization complete:', data);
});

// Async event handling
eventBus.on('asyncAction', async (data) => {
    const result = await fetch('/api/data');
    return result.json();
});

eventBus.emitAsync('asyncAction', { id: 1 }).then(results => {
    console.log('Async results:', results);
});
```

## Exercise: Event Handling System

Create a comprehensive event handling system with:
- Event delegation for dynamic content
- Custom events for component communication
- Event bus for global event management
- Touch and keyboard event handling
- Form validation and submission handling
- Modal and dropdown interactions

## Key Takeaways
- Event delegation reduces memory usage and handles dynamic content
- Use event delegation for better performance with large DOM trees
- Custom events enable loose coupling between components
- Event bus pattern provides centralized event management
- Handle different event types appropriately (mouse, touch, keyboard)
- Use event.preventDefault() and event.stopPropagation() judiciously
- Implement proper error handling in event handlers
- Test event handling across different devices and browsers
- Use event delegation for better maintainability
- Document event handling patterns for team consistency
