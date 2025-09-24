# Dynamic Content Management

## Introduction
Dynamic content management is essential for creating interactive web applications. This lesson covers DOM manipulation, content updates, virtual scrolling, and performance optimization for dynamic content.

## DOM Manipulation Techniques

### Efficient DOM Updates
```javascript
// Efficient DOM manipulation class
class DOMManager {
    constructor() {
        this.updateQueue = [];
        this.isUpdating = false;
    }
    
    // Batch DOM updates for better performance
    batchUpdate(updates) {
        this.updateQueue.push(...updates);
        
        if (!this.isUpdating) {
            this.processUpdateQueue();
        }
    }
    
    async processUpdateQueue() {
        this.isUpdating = true;
        
        while (this.updateQueue.length > 0) {
            const update = this.updateQueue.shift();
            await this.executeUpdate(update);
        }
        
        this.isUpdating = false;
    }
    
    async executeUpdate(update) {
        const { type, element, data } = update;
        
        switch (type) {
            case 'append':
                await this.appendElement(element, data);
                break;
            case 'prepend':
                await this.prependElement(element, data);
                break;
            case 'replace':
                await this.replaceElement(element, data);
                break;
            case 'remove':
                await this.removeElement(element);
                break;
            case 'update':
                await this.updateElement(element, data);
                break;
        }
    }
    
    // Element creation with DocumentFragment
    createElements(template) {
        const fragment = document.createDocumentFragment();
        
        template.forEach(item => {
            const element = this.createElement(item);
            fragment.appendChild(element);
        });
        
        return fragment;
    }
    
    createElement(config) {
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
    
    // Efficient element updates
    updateElement(element, data) {
        if (data.text !== undefined) {
            element.textContent = data.text;
        }
        
        if (data.html !== undefined) {
            element.innerHTML = data.html;
        }
        
        if (data.classes) {
            element.className = data.classes.join(' ');
        }
        
        if (data.attributes) {
            Object.entries(data.attributes).forEach(([key, value]) => {
                element.setAttribute(key, value);
            });
        }
        
        if (data.styles) {
            Object.entries(data.styles).forEach(([property, value]) => {
                element.style[property] = value;
            });
        }
    }
    
    // Element removal with cleanup
    removeElement(element) {
        if (element && element.parentNode) {
            // Remove event listeners
            this.removeEventListeners(element);
            
            // Remove from DOM
            element.parentNode.removeChild(element);
        }
    }
    
    removeEventListeners(element) {
        // Clone element to remove all event listeners
        const newElement = element.cloneNode(true);
        element.parentNode.replaceChild(newElement, element);
    }
    
    // Utility methods
    appendElement(container, data) {
        if (Array.isArray(data)) {
            const fragment = this.createElements(data);
            container.appendChild(fragment);
        } else {
            const element = this.createElement(data);
            container.appendChild(element);
        }
    }
    
    prependElement(container, data) {
        if (Array.isArray(data)) {
            const fragment = this.createElements(data);
            container.insertBefore(fragment, container.firstChild);
        } else {
            const element = this.createElement(data);
            container.insertBefore(element, container.firstChild);
        }
    }
    
    replaceElement(oldElement, data) {
        const newElement = this.createElement(data);
        oldElement.parentNode.replaceChild(newElement, oldElement);
    }
}

// Usage
const domManager = new DOMManager();

// Batch updates
domManager.batchUpdate([
    {
        type: 'append',
        element: document.querySelector('.container'),
        data: {
            tag: 'div',
            classes: ['card'],
            text: 'New card content'
        }
    },
    {
        type: 'update',
        element: document.querySelector('.existing-element'),
        data: {
            text: 'Updated content',
            classes: ['updated']
        }
    }
]);
```

### Content Templating
```javascript
// Template engine for dynamic content
class TemplateEngine {
    constructor() {
        this.templates = new Map();
        this.cache = new Map();
    }
    
    // Register template
    registerTemplate(name, template) {
        this.templates.set(name, template);
    }
    
    // Render template with data
    render(templateName, data) {
        const template = this.templates.get(templateName);
        if (!template) {
            throw new Error(`Template ${templateName} not found`);
        }
        
        // Check cache
        const cacheKey = `${templateName}-${JSON.stringify(data)}`;
        if (this.cache.has(cacheKey)) {
            return this.cache.get(cacheKey).cloneNode(true);
        }
        
        // Render template
        const element = this.renderTemplate(template, data);
        
        // Cache result
        this.cache.set(cacheKey, element.cloneNode(true));
        
        return element;
    }
    
    renderTemplate(template, data) {
        if (typeof template === 'string') {
            return this.renderStringTemplate(template, data);
        } else if (typeof template === 'function') {
            return template(data);
        } else if (template.tag) {
            return this.renderElementTemplate(template, data);
        }
    }
    
    renderStringTemplate(template, data) {
        const div = document.createElement('div');
        div.innerHTML = this.interpolateString(template, data);
        return div.firstElementChild;
    }
    
    renderElementTemplate(template, data) {
        const element = document.createElement(template.tag);
        
        // Set attributes
        if (template.attributes) {
            Object.entries(template.attributes).forEach(([key, value]) => {
                element.setAttribute(key, this.interpolateString(value, data));
            });
        }
        
        // Set classes
        if (template.classes) {
            element.className = template.classes.map(cls => 
                this.interpolateString(cls, data)
            ).join(' ');
        }
        
        // Set content
        if (template.content) {
            if (typeof template.content === 'string') {
                element.innerHTML = this.interpolateString(template.content, data);
            } else if (typeof template.content === 'function') {
                element.innerHTML = template.content(data);
            }
        }
        
        // Add children
        if (template.children) {
            template.children.forEach(child => {
                const childElement = this.renderTemplate(child, data);
                element.appendChild(childElement);
            });
        }
        
        return element;
    }
    
    interpolateString(str, data) {
        return str.replace(/\{\{(\w+)\}\}/g, (match, key) => {
            return data[key] || '';
        });
    }
    
    // Clear cache
    clearCache() {
        this.cache.clear();
    }
}

// Usage
const templateEngine = new TemplateEngine();

// Register templates
templateEngine.registerTemplate('card', {
    tag: 'div',
    classes: ['card'],
    attributes: {
        'data-id': '{{id}}'
    },
    content: `
        <div class="card-header">
            <h3>{{title}}</h3>
        </div>
        <div class="card-body">
            <p>{{description}}</p>
        </div>
        <div class="card-footer">
            <button data-action="edit" data-id="{{id}}">Edit</button>
            <button data-action="delete" data-id="{{id}}">Delete</button>
        </div>
    `
});

templateEngine.registerTemplate('list', {
    tag: 'ul',
    classes: ['list'],
    children: [
        {
            tag: 'li',
            classes: ['list-item'],
            content: '{{item}}'
        }
    ]
});

// Render templates
const cardData = {
    id: 1,
    title: 'Sample Card',
    description: 'This is a sample card'
};

const cardElement = templateEngine.render('card', cardData);
document.querySelector('.container').appendChild(cardElement);
```

## Virtual Scrolling

### Basic Virtual Scrolling
```javascript
// Virtual scrolling implementation
class VirtualScroller {
    constructor(container, options = {}) {
        this.container = container;
        this.itemHeight = options.itemHeight || 50;
        this.items = options.items || [];
        this.visibleStart = 0;
        this.visibleEnd = 0;
        this.containerHeight = 0;
        this.scrollTop = 0;
        
        this.setupVirtualScrolling();
    }
    
    setupVirtualScrolling() {
        this.container.style.overflow = 'auto';
        this.container.style.height = '400px';
        
        this.updateContainerHeight();
        this.updateVisibleRange();
        this.renderVisibleItems();
        
        this.container.addEventListener('scroll', this.throttle(() => {
            this.updateVisibleRange();
            this.renderVisibleItems();
        }, 16));
        
        // Handle resize
        window.addEventListener('resize', this.throttle(() => {
            this.updateContainerHeight();
            this.updateVisibleRange();
            this.renderVisibleItems();
        }, 100));
    }
    
    updateContainerHeight() {
        this.containerHeight = this.container.clientHeight;
    }
    
    updateVisibleRange() {
        this.scrollTop = this.container.scrollTop;
        this.visibleStart = Math.floor(this.scrollTop / this.itemHeight);
        this.visibleEnd = Math.min(
            this.visibleStart + Math.ceil(this.containerHeight / this.itemHeight) + 1,
            this.items.length
        );
    }
    
    renderVisibleItems() {
        const fragment = document.createDocumentFragment();
        
        // Add spacer for items before visible range
        if (this.visibleStart > 0) {
            const spacer = document.createElement('div');
            spacer.style.height = `${this.visibleStart * this.itemHeight}px`;
            fragment.appendChild(spacer);
        }
        
        // Render visible items
        for (let i = this.visibleStart; i < this.visibleEnd; i++) {
            const item = this.createItem(this.items[i], i);
            fragment.appendChild(item);
        }
        
        // Add spacer for items after visible range
        if (this.visibleEnd < this.items.length) {
            const spacer = document.createElement('div');
            spacer.style.height = `${(this.items.length - this.visibleEnd) * this.itemHeight}px`;
            fragment.appendChild(spacer);
        }
        
        this.container.innerHTML = '';
        this.container.appendChild(fragment);
    }
    
    createItem(data, index) {
        const item = document.createElement('div');
        item.style.height = `${this.itemHeight}px`;
        item.style.display = 'flex';
        item.style.alignItems = 'center';
        item.style.padding = '0 1rem';
        item.style.borderBottom = '1px solid #eee';
        
        if (typeof data === 'string') {
            item.textContent = data;
        } else if (typeof data === 'object') {
            item.innerHTML = this.renderItemContent(data);
        }
        
        return item;
    }
    
    renderItemContent(data) {
        return `
            <div class="item-content">
                <h4>${data.title || ''}</h4>
                <p>${data.description || ''}</p>
            </div>
        `;
    }
    
    // Update items
    updateItems(newItems) {
        this.items = newItems;
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Add item
    addItem(item) {
        this.items.push(item);
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Remove item
    removeItem(index) {
        this.items.splice(index, 1);
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Utility method
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

// Usage
const container = document.querySelector('.virtual-scroller');
const items = Array.from({ length: 10000 }, (_, i) => ({
    title: `Item ${i + 1}`,
    description: `Description for item ${i + 1}`
}));

const virtualScroller = new VirtualScroller(container, {
    itemHeight: 60,
    items: items
});
```

### Advanced Virtual Scrolling
```javascript
// Advanced virtual scrolling with dynamic heights
class AdvancedVirtualScroller {
    constructor(container, options = {}) {
        this.container = container;
        this.items = options.items || [];
        this.estimatedItemHeight = options.estimatedItemHeight || 50;
        this.itemHeights = new Map();
        this.visibleStart = 0;
        this.visibleEnd = 0;
        this.containerHeight = 0;
        this.scrollTop = 0;
        
        this.setupVirtualScrolling();
    }
    
    setupVirtualScrolling() {
        this.container.style.overflow = 'auto';
        this.container.style.height = '400px';
        
        this.updateContainerHeight();
        this.updateVisibleRange();
        this.renderVisibleItems();
        
        this.container.addEventListener('scroll', this.throttle(() => {
            this.updateVisibleRange();
            this.renderVisibleItems();
        }, 16));
        
        // Handle resize
        window.addEventListener('resize', this.throttle(() => {
            this.updateContainerHeight();
            this.updateVisibleRange();
            this.renderVisibleItems();
        }, 100));
    }
    
    updateContainerHeight() {
        this.containerHeight = this.container.clientHeight;
    }
    
    updateVisibleRange() {
        this.scrollTop = this.container.scrollTop;
        
        let currentHeight = 0;
        this.visibleStart = 0;
        
        // Find visible start
        for (let i = 0; i < this.items.length; i++) {
            const itemHeight = this.getItemHeight(i);
            if (currentHeight + itemHeight > this.scrollTop) {
                this.visibleStart = i;
                break;
            }
            currentHeight += itemHeight;
        }
        
        // Find visible end
        this.visibleEnd = this.visibleStart;
        for (let i = this.visibleStart; i < this.items.length; i++) {
            const itemHeight = this.getItemHeight(i);
            if (currentHeight > this.scrollTop + this.containerHeight) {
                break;
            }
            currentHeight += itemHeight;
            this.visibleEnd = i + 1;
        }
    }
    
    getItemHeight(index) {
        return this.itemHeights.get(index) || this.estimatedItemHeight;
    }
    
    setItemHeight(index, height) {
        this.itemHeights.set(index, height);
    }
    
    getTotalHeight() {
        let totalHeight = 0;
        for (let i = 0; i < this.items.length; i++) {
            totalHeight += this.getItemHeight(i);
        }
        return totalHeight;
    }
    
    getOffsetTop(index) {
        let offset = 0;
        for (let i = 0; i < index; i++) {
            offset += this.getItemHeight(i);
        }
        return offset;
    }
    
    renderVisibleItems() {
        const fragment = document.createDocumentFragment();
        
        // Add spacer for items before visible range
        if (this.visibleStart > 0) {
            const spacer = document.createElement('div');
            spacer.style.height = `${this.getOffsetTop(this.visibleStart)}px`;
            fragment.appendChild(spacer);
        }
        
        // Render visible items
        for (let i = this.visibleStart; i < this.visibleEnd; i++) {
            const item = this.createItem(this.items[i], i);
            fragment.appendChild(item);
        }
        
        // Add spacer for items after visible range
        if (this.visibleEnd < this.items.length) {
            const spacer = document.createElement('div');
            spacer.style.height = `${this.getTotalHeight() - this.getOffsetTop(this.visibleEnd)}px`;
            fragment.appendChild(spacer);
        }
        
        this.container.innerHTML = '';
        this.container.appendChild(fragment);
    }
    
    createItem(data, index) {
        const item = document.createElement('div');
        item.style.display = 'flex';
        item.style.alignItems = 'center';
        item.style.padding = '0 1rem';
        item.style.borderBottom = '1px solid #eee';
        
        if (typeof data === 'string') {
            item.textContent = data;
        } else if (typeof data === 'object') {
            item.innerHTML = this.renderItemContent(data);
        }
        
        // Measure item height
        const observer = new ResizeObserver(entries => {
            for (let entry of entries) {
                const height = entry.contentRect.height;
                this.setItemHeight(index, height);
            }
        });
        
        observer.observe(item);
        
        return item;
    }
    
    renderItemContent(data) {
        return `
            <div class="item-content">
                <h4>${data.title || ''}</h4>
                <p>${data.description || ''}</p>
                ${data.image ? `<img src="${data.image}" alt="${data.title}" style="max-width: 100px; height: auto;">` : ''}
            </div>
        `;
    }
    
    // Update items
    updateItems(newItems) {
        this.items = newItems;
        this.itemHeights.clear();
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Add item
    addItem(item) {
        this.items.push(item);
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Remove item
    removeItem(index) {
        this.items.splice(index, 1);
        this.itemHeights.delete(index);
        this.updateVisibleRange();
        this.renderVisibleItems();
    }
    
    // Utility method
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

// Usage
const container = document.querySelector('.advanced-virtual-scroller');
const items = Array.from({ length: 1000 }, (_, i) => ({
    title: `Item ${i + 1}`,
    description: `Description for item ${i + 1}`,
    image: i % 3 === 0 ? `https://picsum.photos/100/100?random=${i}` : null
}));

const advancedVirtualScroller = new AdvancedVirtualScroller(container, {
    estimatedItemHeight: 80,
    items: items
});
```

## Exercise: Dynamic Content Management System

Create a dynamic content management system with:
- Efficient DOM manipulation with batching
- Template engine for content rendering
- Virtual scrolling for large datasets
- Event delegation for dynamic content
- Performance monitoring and optimization
- Content caching and lazy loading

## Key Takeaways
- Use DocumentFragment for efficient DOM manipulation
- Batch DOM updates to improve performance
- Implement virtual scrolling for large datasets
- Use template engines for consistent content rendering
- Cache rendered content to avoid re-computation
- Monitor performance and optimize accordingly
- Use event delegation for dynamic content
- Implement proper cleanup for removed elements
- Test with large datasets to ensure performance
- Use modern APIs like ResizeObserver for dynamic sizing
