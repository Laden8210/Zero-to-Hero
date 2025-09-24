# Client-Side Rendering (CSR)

## Introduction
Client-Side Rendering (CSR) is a rendering approach where the browser downloads JavaScript and renders the page dynamically. This lesson covers CSR implementation, optimization techniques, and best practices.

## CSR Implementation

### Basic CSR Setup
```javascript
// Basic CSR implementation
class CSRApp {
    constructor() {
        this.setupApp();
        this.setupRouting();
        this.setupStateManagement();
    }
    
    setupApp() {
        // Create root element
        this.root = document.getElementById('root');
        
        // Set up event listeners
        this.setupEventListeners();
        
        // Initialize app
        this.render();
    }
    
    setupRouting() {
        this.routes = {
            '/': () => this.renderHome(),
            '/about': () => this.renderAbout(),
            '/contact': () => this.renderContact(),
            '/users': () => this.renderUsers()
        };
        
        // Handle navigation
        window.addEventListener('popstate', () => {
            this.handleRouteChange();
        });
        
        // Handle link clicks
        document.addEventListener('click', (e) => {
            if (e.target.matches('a[href^="/"]')) {
                e.preventDefault();
                this.navigateTo(e.target.href);
            }
        });
    }
    
    setupStateManagement() {
        this.state = {
            currentRoute: window.location.pathname,
            user: null,
            data: {},
            loading: false
        };
        
        // State change listeners
        this.stateListeners = [];
    }
    
    setupEventListeners() {
        // Global event listeners
        window.addEventListener('resize', this.debounce(() => {
            this.handleResize();
        }, 250));
        
        window.addEventListener('scroll', this.throttle(() => {
            this.handleScroll();
        }, 16));
    }
    
    navigateTo(url) {
        const pathname = new URL(url).pathname;
        
        if (this.state.currentRoute !== pathname) {
            this.state.currentRoute = pathname;
            history.pushState(null, '', url);
            this.handleRouteChange();
        }
    }
    
    handleRouteChange() {
        const route = this.routes[this.state.currentRoute];
        
        if (route) {
            this.setState({ loading: true });
            route().then(() => {
                this.setState({ loading: false });
            });
        } else {
            this.render404();
        }
    }
    
    setState(newState) {
        this.state = { ...this.state, ...newState };
        this.notifyStateChange();
    }
    
    notifyStateChange() {
        this.stateListeners.forEach(listener => {
            listener(this.state);
        });
    }
    
    subscribe(listener) {
        this.stateListeners.push(listener);
        
        return () => {
            const index = this.stateListeners.indexOf(listener);
            if (index > -1) {
                this.stateListeners.splice(index, 1);
            }
        };
    }
    
    render() {
        this.root.innerHTML = this.getAppHTML();
        this.handleRouteChange();
    }
    
    getAppHTML() {
        return `
            <div class="app">
                <nav class="navigation">
                    <a href="/">Home</a>
                    <a href="/about">About</a>
                    <a href="/contact">Contact</a>
                    <a href="/users">Users</a>
                </nav>
                
                <main class="main-content">
                    <div id="content"></div>
                </main>
                
                ${this.state.loading ? '<div class="loading">Loading...</div>' : ''}
            </div>
        `;
    }
    
    renderHome() {
        const content = document.getElementById('content');
        content.innerHTML = `
            <h1>Welcome to CSR App</h1>
            <p>This is a client-side rendered application.</p>
            <button onclick="app.loadData()">Load Data</button>
            <div id="data-container"></div>
        `;
    }
    
    renderAbout() {
        const content = document.getElementById('content');
        content.innerHTML = `
            <h1>About Us</h1>
            <p>Learn more about our company and mission.</p>
        `;
    }
    
    renderContact() {
        const content = document.getElementById('content');
        content.innerHTML = `
            <h1>Contact Us</h1>
            <form onsubmit="app.handleContactSubmit(event)">
                <input type="text" name="name" placeholder="Name" required>
                <input type="email" name="email" placeholder="Email" required>
                <textarea name="message" placeholder="Message" required></textarea>
                <button type="submit">Send</button>
            </form>
        `;
    }
    
    async renderUsers() {
        const content = document.getElementById('content');
        content.innerHTML = '<div class="loading">Loading users...</div>';
        
        try {
            const users = await this.fetchUsers();
            content.innerHTML = `
                <h1>Users</h1>
                <div class="users-list">
                    ${users.map(user => `
                        <div class="user-card">
                            <h3>${user.name}</h3>
                            <p>${user.email}</p>
                        </div>
                    `).join('')}
                </div>
            `;
        } catch (error) {
            content.innerHTML = '<div class="error">Failed to load users</div>';
        }
    }
    
    render404() {
        const content = document.getElementById('content');
        content.innerHTML = `
            <h1>404 - Page Not Found</h1>
            <p>The page you're looking for doesn't exist.</p>
            <a href="/">Go Home</a>
        `;
    }
    
    async loadData() {
        this.setState({ loading: true });
        
        try {
            const data = await this.fetchData();
            this.setState({ data });
            
            const container = document.getElementById('data-container');
            container.innerHTML = `
                <h3>Loaded Data</h3>
                <pre>${JSON.stringify(data, null, 2)}</pre>
            `;
        } catch (error) {
            console.error('Failed to load data:', error);
        } finally {
            this.setState({ loading: false });
        }
    }
    
    async handleContactSubmit(event) {
        event.preventDefault();
        
        const formData = new FormData(event.target);
        const data = Object.fromEntries(formData);
        
        try {
            await this.submitContactForm(data);
            alert('Message sent successfully!');
            event.target.reset();
        } catch (error) {
            alert('Failed to send message. Please try again.');
        }
    }
    
    async fetchData() {
        const response = await fetch('/api/data');
        if (!response.ok) {
            throw new Error('Failed to fetch data');
        }
        return response.json();
    }
    
    async fetchUsers() {
        const response = await fetch('/api/users');
        if (!response.ok) {
            throw new Error('Failed to fetch users');
        }
        return response.json();
    }
    
    async submitContactForm(data) {
        const response = await fetch('/api/contact', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(data)
        });
        
        if (!response.ok) {
            throw new Error('Failed to submit contact form');
        }
    }
    
    handleResize() {
        // Handle window resize
        console.log('Window resized');
    }
    
    handleScroll() {
        // Handle scroll events
        const scrollTop = window.pageYOffset;
        
        // Update header based on scroll position
        const header = document.querySelector('.header');
        if (header) {
            header.classList.toggle('scrolled', scrollTop > 100);
        }
    }
    
    debounce(func, wait) {
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
    
    throttle(func, limit) {
        let inThrottle;
        return function executedFunction(...args) {
            if (!inThrottle) {
                func.apply(this, args);
                inThrottle = true;
                setTimeout(() => inThrottle = false, limit);
            }
        };
    }
}

// Initialize app
const app = new CSRApp();
```

**Code Explanation:**
- `class CSRApp`: Creates a class to manage client-side rendering
- `constructor()`: Initializes the app with routing and state management
- `document.getElementById('root')`: Gets the root DOM element for rendering
- `this.routes = {...}`: Defines route handlers for different paths
- `window.addEventListener('popstate', ...)`: Handles browser back/forward navigation
- `e.target.matches('a[href^="/"]')`: Checks if clicked element is an internal link
- `e.preventDefault()`: Prevents default link navigation
- `history.pushState(null, '', url)`: Updates browser URL without page reload
- `this.setState({...})`: Updates application state
- `this.debounce(() => {...}, 250)`: Debounces resize events to prevent excessive calls
- `this.throttle(() => {...}, 16)`: Throttles scroll events to 60fps
- `{ ...this.state, ...newState }`: Spreads existing state with new state updates
- `this.root.innerHTML = this.getAppHTML()`: Updates DOM with new HTML content
- `content.innerHTML = ...`: Updates specific content areas dynamically
- `await this.fetchUsers()`: Fetches data asynchronously from API
- `users.map(user => ...).join('')`: Creates HTML for each user item
- `new FormData(event.target)`: Extracts form data from submitted form
- `Object.fromEntries(formData)`: Converts FormData to JavaScript object

**Benefits:**
- Provides smooth single-page application experience
- Enables client-side routing without page reloads
- Supports state management for complex applications
- Optimizes performance with debounced/throttled events
- Maintains browser history for proper navigation
- Handles dynamic content loading and form submissions

### Advanced CSR with Virtual DOM
```javascript
// Virtual DOM implementation for CSR
class VirtualDOM {
    constructor() {
        this.root = null;
        this.currentTree = null;
    }
    
    createElement(tag, props = {}, ...children) {
        return {
            tag,
            props,
            children: children.flat()
        };
    }
    
    render(vnode, container) {
        const domNode = this.createDOMNode(vnode);
        container.appendChild(domNode);
        return domNode;
    }
    
    createDOMNode(vnode) {
        if (typeof vnode === 'string') {
            return document.createTextNode(vnode);
        }
        
        const { tag, props, children } = vnode;
        const element = document.createElement(tag);
        
        // Set properties
        if (props) {
            Object.entries(props).forEach(([key, value]) => {
                if (key === 'className') {
                    element.className = value;
                } else if (key.startsWith('on')) {
                    const eventName = key.slice(2).toLowerCase();
                    element.addEventListener(eventName, value);
                } else {
                    element.setAttribute(key, value);
                }
            });
        }
        
        // Render children
        if (children) {
            children.forEach(child => {
                const childNode = this.createDOMNode(child);
                element.appendChild(childNode);
            });
        }
        
        return element;
    }
    
    diff(oldTree, newTree) {
        const patches = [];
        this.diffNodes(oldTree, newTree, 0, patches);
        return patches;
    }
    
    diffNodes(oldNode, newNode, index, patches) {
        if (!oldNode) {
            patches.push({ type: 'CREATE', index, newNode });
        } else if (!newNode) {
            patches.push({ type: 'REMOVE', index });
        } else if (this.isDifferent(oldNode, newNode)) {
            patches.push({ type: 'REPLACE', index, newNode });
        } else if (newNode.tag) {
            this.diffProps(oldNode.props, newNode.props, index, patches);
            this.diffChildren(oldNode.children, newNode.children, index, patches);
        }
    }
    
    diffProps(oldProps, newProps, index, patches) {
        const allProps = { ...oldProps, ...newProps };
        
        Object.keys(allProps).forEach(key => {
            if (oldProps[key] !== newProps[key]) {
                patches.push({
                    type: 'PROPS',
                    index,
                    props: { [key]: newProps[key] }
                });
            }
        });
    }
    
    diffChildren(oldChildren, newChildren, index, patches) {
        const maxLength = Math.max(oldChildren.length, newChildren.length);
        
        for (let i = 0; i < maxLength; i++) {
            this.diffNodes(
                oldChildren[i],
                newChildren[i],
                index + i,
                patches
            );
        }
    }
    
    isDifferent(oldNode, newNode) {
        return (
            typeof oldNode !== typeof newNode ||
            (typeof oldNode === 'string' && oldNode !== newNode) ||
            (oldNode.tag !== newNode.tag)
        );
    }
    
    patch(container, patches) {
        patches.forEach(patch => {
            const element = container.childNodes[patch.index];
            
            switch (patch.type) {
                case 'CREATE':
                    const newNode = this.createDOMNode(patch.newNode);
                    container.insertBefore(newNode, element);
                    break;
                case 'REMOVE':
                    container.removeChild(element);
                    break;
                case 'REPLACE':
                    const newElement = this.createDOMNode(patch.newNode);
                    container.replaceChild(newElement, element);
                    break;
                case 'PROPS':
                    Object.entries(patch.props).forEach(([key, value]) => {
                        if (key === 'className') {
                            element.className = value;
                        } else {
                            element.setAttribute(key, value);
                        }
                    });
                    break;
            }
        });
    }
}

// CSR App with Virtual DOM
class CSRAppWithVDOM {
    constructor() {
        this.vdom = new VirtualDOM();
        this.state = {
            currentRoute: window.location.pathname,
            data: null,
            loading: false
        };
        
        this.setupApp();
        this.setupRouting();
    }
    
    setupApp() {
        this.root = document.getElementById('root');
        this.render();
    }
    
    setupRouting() {
        this.routes = {
            '/': () => this.renderHome(),
            '/about': () => this.renderAbout(),
            '/users': () => this.renderUsers()
        };
        
        window.addEventListener('popstate', () => {
            this.handleRouteChange();
        });
        
        document.addEventListener('click', (e) => {
            if (e.target.matches('a[href^="/"]')) {
                e.preventDefault();
                this.navigateTo(e.target.href);
            }
        });
    }
    
    navigateTo(url) {
        const pathname = new URL(url).pathname;
        
        if (this.state.currentRoute !== pathname) {
            this.setState({ currentRoute: pathname });
            history.pushState(null, '', url);
            this.handleRouteChange();
        }
    }
    
    setState(newState) {
        this.state = { ...this.state, ...newState };
        this.render();
    }
    
    handleRouteChange() {
        const route = this.routes[this.state.currentRoute];
        
        if (route) {
            route();
        } else {
            this.render404();
        }
    }
    
    render() {
        const newTree = this.getAppTree();
        
        if (this.currentTree) {
            const patches = this.vdom.diff(this.currentTree, newTree);
            this.vdom.patch(this.root, patches);
        } else {
            this.vdom.render(newTree, this.root);
        }
        
        this.currentTree = newTree;
    }
    
    getAppTree() {
        return this.vdom.createElement('div', { className: 'app' },
            this.vdom.createElement('nav', { className: 'navigation' },
                this.vdom.createElement('a', { href: '/', className: 'nav-link' }, 'Home'),
                this.vdom.createElement('a', { href: '/about', className: 'nav-link' }, 'About'),
                this.vdom.createElement('a', { href: '/users', className: 'nav-link' }, 'Users')
            ),
            this.vdom.createElement('main', { className: 'main-content' },
                this.getContentTree()
            ),
            this.state.loading ? this.vdom.createElement('div', { className: 'loading' }, 'Loading...') : null
        );
    }
    
    getContentTree() {
        const route = this.routes[this.state.currentRoute];
        
        if (route) {
            return route();
        } else {
            return this.render404();
        }
    }
    
    renderHome() {
        return this.vdom.createElement('div', { className: 'home' },
            this.vdom.createElement('h1', null, 'Welcome to CSR App'),
            this.vdom.createElement('p', null, 'This is a client-side rendered application with Virtual DOM.'),
            this.vdom.createElement('button', {
                onClick: () => this.loadData()
            }, 'Load Data'),
            this.state.data ? this.vdom.createElement('div', { className: 'data' },
                this.vdom.createElement('h3', null, 'Loaded Data'),
                this.vdom.createElement('pre', null, JSON.stringify(this.state.data, null, 2))
            ) : null
        );
    }
    
    renderAbout() {
        return this.vdom.createElement('div', { className: 'about' },
            this.vdom.createElement('h1', null, 'About Us'),
            this.vdom.createElement('p', null, 'Learn more about our company and mission.')
        );
    }
    
    async renderUsers() {
        this.setState({ loading: true });
        
        try {
            const users = await this.fetchUsers();
            this.setState({ users, loading: false });
        } catch (error) {
            this.setState({ error: 'Failed to load users', loading: false });
        }
    }
    
    render404() {
        return this.vdom.createElement('div', { className: 'error' },
            this.vdom.createElement('h1', null, '404 - Page Not Found'),
            this.vdom.createElement('p', null, 'The page you\'re looking for doesn\'t exist.'),
            this.vdom.createElement('a', { href: '/' }, 'Go Home')
        );
    }
    
    async loadData() {
        this.setState({ loading: true });
        
        try {
            const data = await this.fetchData();
            this.setState({ data, loading: false });
        } catch (error) {
            this.setState({ error: 'Failed to load data', loading: false });
        }
    }
    
    async fetchData() {
        const response = await fetch('/api/data');
        if (!response.ok) {
            throw new Error('Failed to fetch data');
        }
        return response.json();
    }
    
    async fetchUsers() {
        const response = await fetch('/api/users');
        if (!response.ok) {
            throw new Error('Failed to fetch users');
        }
        return response.json();
    }
}

// Initialize app
const app = new CSRAppWithVDOM();
```

## CSR Optimization Techniques

### Code Splitting and Lazy Loading
```javascript
// CSR with code splitting and lazy loading
class OptimizedCSRApp {
    constructor() {
        this.setupApp();
        this.setupCodeSplitting();
        this.setupLazyLoading();
    }
    
    setupApp() {
        this.root = document.getElementById('root');
        this.state = {
            currentRoute: window.location.pathname,
            loadedComponents: new Map(),
            loading: false
        };
        
        this.setupRouting();
        this.render();
    }
    
    setupCodeSplitting() {
        this.routes = {
            '/': () => this.loadComponent('Home'),
            '/about': () => this.loadComponent('About'),
            '/users': () => this.loadComponent('Users'),
            '/dashboard': () => this.loadComponent('Dashboard'),
            '/profile': () => this.loadComponent('Profile')
        };
    }
    
    setupLazyLoading() {
        // Lazy load images
        this.lazyLoadImages();
        
        // Lazy load components
        this.lazyLoadComponents();
        
        // Preload next route
        this.preloadNextRoute();
    }
    
    async loadComponent(componentName) {
        if (this.state.loadedComponents.has(componentName)) {
            return this.state.loadedComponents.get(componentName);
        }
        
        this.setState({ loading: true });
        
        try {
            const component = await this.importComponent(componentName);
            this.state.loadedComponents.set(componentName, component);
            this.setState({ loading: false });
            return component;
        } catch (error) {
            console.error(`Failed to load component ${componentName}:`, error);
            this.setState({ loading: false });
            return null;
        }
    }
    
    async importComponent(componentName) {
        // Dynamic import with webpack magic comments
        const module = await import(
            /* webpackChunkName: "[request]" */
            /* webpackMode: "lazy" */
            `./components/${componentName}`
        );
        
        return module.default;
    }
    
    lazyLoadImages() {
        const images = document.querySelectorAll('img[data-src]');
        
        const imageObserver = new IntersectionObserver((entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.dataset.src;
                    img.classList.remove('lazy');
                    imageObserver.unobserve(img);
                }
            });
        });
        
        images.forEach((img) => imageObserver.observe(img));
    }
    
    lazyLoadComponents() {
        const components = document.querySelectorAll('[data-component]');
        
        const componentObserver = new IntersectionObserver((entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    const componentName = entry.target.dataset.component;
                    this.loadComponent(componentName);
                    componentObserver.unobserve(entry.target);
                }
            });
        });
        
        components.forEach((component) => componentObserver.observe(component));
    }
    
    preloadNextRoute() {
        const links = document.querySelectorAll('a[href^="/"]');
        
        links.forEach((link) => {
            link.addEventListener('mouseenter', () => {
                const href = link.getAttribute('href');
                const route = this.routes[href];
                
                if (route) {
                    // Preload the component
                    this.preloadComponent(href);
                }
            });
        });
    }
    
    async preloadComponent(route) {
        const componentName = this.getComponentNameFromRoute(route);
        
        if (!this.state.loadedComponents.has(componentName)) {
            try {
                await this.importComponent(componentName);
            } catch (error) {
                console.error(`Failed to preload component ${componentName}:`, error);
            }
        }
    }
    
    getComponentNameFromRoute(route) {
        const routeMap = {
            '/': 'Home',
            '/about': 'About',
            '/users': 'Users',
            '/dashboard': 'Dashboard',
            '/profile': 'Profile'
        };
        
        return routeMap[route];
    }
    
    navigateTo(url) {
        const pathname = new URL(url).pathname;
        
        if (this.state.currentRoute !== pathname) {
            this.setState({ currentRoute: pathname });
            history.pushState(null, '', url);
            this.handleRouteChange();
        }
    }
    
    setState(newState) {
        this.state = { ...this.state, ...newState };
        this.render();
    }
    
    handleRouteChange() {
        const route = this.routes[this.state.currentRoute];
        
        if (route) {
            route();
        } else {
            this.render404();
        }
    }
    
    render() {
        this.root.innerHTML = this.getAppHTML();
        this.handleRouteChange();
    }
    
    getAppHTML() {
        return `
            <div class="app">
                <nav class="navigation">
                    <a href="/">Home</a>
                    <a href="/about">About</a>
                    <a href="/users">Users</a>
                    <a href="/dashboard">Dashboard</a>
                    <a href="/profile">Profile</a>
                </nav>
                
                <main class="main-content">
                    <div id="content"></div>
                </main>
                
                ${this.state.loading ? '<div class="loading">Loading...</div>' : ''}
            </div>
        `;
    }
    
    render404() {
        const content = document.getElementById('content');
        content.innerHTML = `
            <h1>404 - Page Not Found</h1>
            <p>The page you're looking for doesn't exist.</p>
            <a href="/">Go Home</a>
        `;
    }
}

// Initialize optimized app
const optimizedApp = new OptimizedCSRApp();
```

## Exercise: CSR Application

Create a comprehensive CSR application with:
- Basic CSR implementation
- Virtual DOM integration
- Code splitting and lazy loading
- State management
- Routing system
- Performance optimization

## Key Takeaways
- CSR provides dynamic, interactive user experiences
- Use Virtual DOM for efficient updates
- Implement code splitting for better performance
- Use lazy loading for images and components
- Preload next routes for better UX
- Monitor performance and optimize accordingly
- Use modern JavaScript features for better performance
- Implement proper error handling
- Test CSR applications across different browsers
- Consider SEO implications of CSR
