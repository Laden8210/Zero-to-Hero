# CSS Custom Properties (Variables)

## Introduction
CSS custom properties (CSS variables) provide a powerful way to create dynamic, maintainable stylesheets. This lesson covers variable syntax, scoping, theming, and advanced usage patterns.

## Basic CSS Custom Properties

### Variable Declaration and Usage
```css
/* Global variables */
:root {
    --primary-color: #007bff;
    --secondary-color: #6c757d;
    --success-color: #28a745;
    --danger-color: #dc3545;
    --warning-color: #ffc107;
    --info-color: #17a2b8;
    
    --font-family: 'Inter', sans-serif;
    --font-size-base: 1rem;
    --font-size-lg: 1.125rem;
    --font-size-sm: 0.875rem;
    
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 3rem;
    
    --border-radius: 0.375rem;
    --border-radius-lg: 0.5rem;
    --border-radius-xl: 1rem;
    
    --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
    --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
    --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
}

/* Using variables */
.button {
    background-color: var(--primary-color);
    color: white;
    padding: var(--spacing-sm) var(--spacing-md);
    border-radius: var(--border-radius);
    font-family: var(--font-family);
    font-size: var(--font-size-base);
    box-shadow: var(--shadow-sm);
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
}

.button:hover {
    background-color: color-mix(in srgb, var(--primary-color) 80%, black);
    box-shadow: var(--shadow-md);
}

.button-secondary {
    background-color: var(--secondary-color);
}

.button-success {
    background-color: var(--success-color);
}

.button-danger {
    background-color: var(--danger-color);
}
```

### Variable Scoping
```css
/* Global scope */
:root {
    --global-color: #333;
    --global-size: 1rem;
}

/* Component scope */
.card {
    --card-bg: white;
    --card-padding: 1rem;
    --card-radius: 8px;
    --card-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    
    background: var(--card-bg);
    padding: var(--card-padding);
    border-radius: var(--card-radius);
    box-shadow: var(--card-shadow);
}

/* Nested component scope */
.card-header {
    --header-bg: #f8f9fa;
    --header-padding: 0.75rem;
    
    background: var(--header-bg);
    padding: var(--header-padding);
    border-bottom: 1px solid #dee2e6;
}

/* Local scope override */
.card.featured {
    --card-bg: #fff3cd;
    --card-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

/* Media query scope */
@media (prefers-color-scheme: dark) {
    :root {
        --global-color: #fff;
        --card-bg: #2d3748;
    }
}
```

## Dynamic Theming

### Theme Switching
```css
/* Light theme */
:root {
    --bg-primary: #ffffff;
    --bg-secondary: #f8f9fa;
    --text-primary: #212529;
    --text-secondary: #6c757d;
    --border-color: #dee2e6;
    --shadow-color: rgba(0, 0, 0, 0.1);
}

/* Dark theme */
[data-theme="dark"] {
    --bg-primary: #1a1a1a;
    --bg-secondary: #2d2d2d;
    --text-primary: #ffffff;
    --text-secondary: #adb5bd;
    --border-color: #495057;
    --shadow-color: rgba(0, 0, 0, 0.3);
}

/* High contrast theme */
[data-theme="high-contrast"] {
    --bg-primary: #ffffff;
    --bg-secondary: #ffffff;
    --text-primary: #000000;
    --text-secondary: #000000;
    --border-color: #000000;
    --shadow-color: rgba(0, 0, 0, 0.5);
}

/* Component using theme variables */
.app {
    background-color: var(--bg-primary);
    color: var(--text-primary);
    min-height: 100vh;
}

.card {
    background-color: var(--bg-primary);
    color: var(--text-primary);
    border: 1px solid var(--border-color);
    box-shadow: 0 2px 4px var(--shadow-color);
}

.text-primary {
    color: var(--text-primary);
}

.text-secondary {
    color: var(--text-secondary);
}
```

### JavaScript Theme Control
```javascript
// Theme management
class ThemeManager {
    constructor() {
        this.currentTheme = localStorage.getItem('theme') || 'light';
        this.applyTheme(this.currentTheme);
    }
    
    applyTheme(theme) {
        document.documentElement.setAttribute('data-theme', theme);
        this.currentTheme = theme;
        localStorage.setItem('theme', theme);
    }
    
    toggleTheme() {
        const themes = ['light', 'dark', 'high-contrast'];
        const currentIndex = themes.indexOf(this.currentTheme);
        const nextIndex = (currentIndex + 1) % themes.length;
        this.applyTheme(themes[nextIndex]);
    }
    
    setTheme(theme) {
        if (['light', 'dark', 'high-contrast'].includes(theme)) {
            this.applyTheme(theme);
        }
    }
}

// Usage
const themeManager = new ThemeManager();

// Theme toggle button
document.getElementById('theme-toggle').addEventListener('click', () => {
    themeManager.toggleTheme();
});

// System preference detection
if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
    themeManager.setTheme('dark');
}
```

## Advanced CSS Variables

### Variable Calculations
```css
/* Mathematical operations with variables */
:root {
    --base-size: 16px;
    --scale-factor: 1.25;
    --spacing-unit: 8px;
}

/* Typography scale */
.text-xs { font-size: calc(var(--base-size) * 0.75); }
.text-sm { font-size: calc(var(--base-size) * 0.875); }
.text-base { font-size: var(--base-size); }
.text-lg { font-size: calc(var(--base-size) * var(--scale-factor)); }
.text-xl { font-size: calc(var(--base-size) * var(--scale-factor) * var(--scale-factor)); }
.text-2xl { font-size: calc(var(--base-size) * var(--scale-factor) * var(--scale-factor) * var(--scale-factor)); }

/* Spacing scale */
.spacing-xs { margin: var(--spacing-unit); }
.spacing-sm { margin: calc(var(--spacing-unit) * 2); }
.spacing-md { margin: calc(var(--spacing-unit) * 3); }
.spacing-lg { margin: calc(var(--spacing-unit) * 4); }
.spacing-xl { margin: calc(var(--spacing-unit) * 6); }

/* Color calculations */
:root {
    --primary-hue: 210;
    --primary-saturation: 100%;
    --primary-lightness: 50%;
    --primary-color: hsl(var(--primary-hue), var(--primary-saturation), var(--primary-lightness));
}

.button-primary {
    background-color: var(--primary-color);
}

.button-primary:hover {
    background-color: hsl(var(--primary-hue), var(--primary-saturation), calc(var(--primary-lightness) - 10%));
}

.button-primary:active {
    background-color: hsl(var(--primary-hue), var(--primary-saturation), calc(var(--primary-lightness) - 20%));
}
```

### Variable Inheritance and Fallbacks
```css
/* Variable inheritance */
:root {
    --primary-color: #007bff;
    --secondary-color: #6c757d;
}

.component {
    --component-primary: var(--primary-color);
    --component-secondary: var(--secondary-color);
}

.component-variant {
    --component-primary: #28a745; /* Override inherited variable */
}

/* Fallback values */
.button {
    background-color: var(--button-bg, var(--primary-color, #007bff));
    color: var(--button-text, white);
    padding: var(--button-padding, 0.5rem 1rem);
    border-radius: var(--button-radius, 0.375rem);
}

/* Multiple fallbacks */
.text {
    font-family: var(--font-family, 'Inter', sans-serif, system-ui);
    font-size: var(--font-size, 1rem);
    line-height: var(--line-height, 1.5);
}
```

### Dynamic Variable Updates
```css
/* CSS-only dynamic updates */
:root {
    --mouse-x: 0px;
    --mouse-y: 0px;
}

.interactive-element {
    transform: translate(var(--mouse-x), var(--mouse-y));
    transition: transform 0.1s ease;
}

/* JavaScript-controlled variables */
.dynamic-element {
    --rotation: 0deg;
    --scale: 1;
    --opacity: 1;
    
    transform: rotate(var(--rotation)) scale(var(--scale));
    opacity: var(--opacity);
    transition: all 0.3s ease;
}
```

```javascript
// JavaScript variable updates
function updateDynamicElement(element, properties) {
    Object.entries(properties).forEach(([property, value]) => {
        element.style.setProperty(`--${property}`, value);
    });
}

// Usage
const element = document.querySelector('.dynamic-element');

// Animate rotation
updateDynamicElement(element, { rotation: '45deg' });

// Animate scale
updateDynamicElement(element, { scale: '1.2' });

// Animate opacity
updateDynamicElement(element, { opacity: '0.8' });

// Mouse tracking
document.addEventListener('mousemove', (e) => {
    const x = (e.clientX / window.innerWidth) * 100;
    const y = (e.clientY / window.innerHeight) * 100;
    
    document.documentElement.style.setProperty('--mouse-x', `${x}px`);
    document.documentElement.style.setProperty('--mouse-y', `${y}px`);
});
```

## CSS Variables in Component Libraries

### Design System Variables
```css
/* Design system foundation */
:root {
    /* Color system */
    --color-primary-50: #eff6ff;
    --color-primary-100: #dbeafe;
    --color-primary-200: #bfdbfe;
    --color-primary-300: #93c5fd;
    --color-primary-400: #60a5fa;
    --color-primary-500: #3b82f6;
    --color-primary-600: #2563eb;
    --color-primary-700: #1d4ed8;
    --color-primary-800: #1e40af;
    --color-primary-900: #1e3a8a;
    
    /* Typography scale */
    --font-size-xs: 0.75rem;
    --font-size-sm: 0.875rem;
    --font-size-base: 1rem;
    --font-size-lg: 1.125rem;
    --font-size-xl: 1.25rem;
    --font-size-2xl: 1.5rem;
    --font-size-3xl: 1.875rem;
    --font-size-4xl: 2.25rem;
    
    /* Spacing scale */
    --space-0: 0;
    --space-1: 0.25rem;
    --space-2: 0.5rem;
    --space-3: 0.75rem;
    --space-4: 1rem;
    --space-5: 1.25rem;
    --space-6: 1.5rem;
    --space-8: 2rem;
    --space-10: 2.5rem;
    --space-12: 3rem;
    
    /* Border radius */
    --radius-none: 0;
    --radius-sm: 0.125rem;
    --radius-base: 0.25rem;
    --radius-md: 0.375rem;
    --radius-lg: 0.5rem;
    --radius-xl: 0.75rem;
    --radius-2xl: 1rem;
    --radius-full: 9999px;
    
    /* Shadows */
    --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
    --shadow-base: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
    --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
    --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
    --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
}

/* Component system using variables */
.btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: var(--space-2) var(--space-4);
    font-size: var(--font-size-sm);
    font-weight: 500;
    border-radius: var(--radius-md);
    border: 1px solid transparent;
    cursor: pointer;
    transition: all 0.2s ease;
}

.btn-primary {
    background-color: var(--color-primary-500);
    color: white;
}

.btn-primary:hover {
    background-color: var(--color-primary-600);
}

.btn-secondary {
    background-color: var(--color-primary-100);
    color: var(--color-primary-700);
    border-color: var(--color-primary-200);
}

.btn-secondary:hover {
    background-color: var(--color-primary-200);
}

.btn-lg {
    padding: var(--space-3) var(--space-6);
    font-size: var(--font-size-base);
    border-radius: var(--radius-lg);
}

.btn-sm {
    padding: var(--space-1) var(--space-3);
    font-size: var(--font-size-xs);
    border-radius: var(--radius-sm);
}
```

### Responsive Variables
```css
/* Responsive design with variables */
:root {
    --container-padding: 1rem;
    --grid-gap: 1rem;
    --font-size-heading: 1.5rem;
}

@media (min-width: 768px) {
    :root {
        --container-padding: 2rem;
        --grid-gap: 2rem;
        --font-size-heading: 2rem;
    }
}

@media (min-width: 1024px) {
    :root {
        --container-padding: 3rem;
        --grid-gap: 3rem;
        --font-size-heading: 2.5rem;
    }
}

.container {
    padding: var(--container-padding);
}

.grid {
    display: grid;
    gap: var(--grid-gap);
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
}

.heading {
    font-size: var(--font-size-heading);
}
```

## Exercise: CSS Variables Design System

Create a design system using CSS custom properties:
- Color system with multiple shades
- Typography scale with consistent sizing
- Spacing system with logical progression
- Component library using variables
- Theme switching functionality
- Responsive variable updates

## Key Takeaways
- CSS custom properties provide powerful theming and maintainability
- Use :root for global variables, component selectors for scoped variables
- Variables support inheritance and can be overridden
- Use fallback values for better browser compatibility
- JavaScript can dynamically update CSS variables
- Create design systems with consistent variable naming
- Use variables for responsive design and theme switching
- Variables work with calc() for dynamic calculations
- Test variable usage across different browsers
- Document your variable system for team consistency
