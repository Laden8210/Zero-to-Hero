# Component Styling Patterns

## Introduction
Tailwind CSS provides flexible patterns for component styling, from utility-first approaches to component extraction. This lesson covers component patterns, dynamic classes, and maintaining Tailwind's benefits.

## Extracting Component Classes

### @apply Directive
```css
/* styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Component classes using @apply */
@layer components {
    .btn {
        @apply inline-flex items-center justify-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-offset-2 transition-colors duration-200;
    }
    
    .btn-primary {
        @apply bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500;
    }
    
    .btn-secondary {
        @apply bg-gray-200 text-gray-900 hover:bg-gray-300 focus:ring-gray-500;
    }
    
    .btn-danger {
        @apply bg-red-600 text-white hover:bg-red-700 focus:ring-red-500;
    }
    
    .btn-sm {
        @apply px-3 py-1.5 text-xs;
    }
    
    .btn-lg {
        @apply px-6 py-3 text-base;
    }
    
    .card {
        @apply bg-white rounded-lg shadow-md overflow-hidden;
    }
    
    .card-header {
        @apply px-6 py-4 border-b border-gray-200 bg-gray-50;
    }
    
    .card-body {
        @apply px-6 py-4;
    }
    
    .card-footer {
        @apply px-6 py-4 border-t border-gray-200 bg-gray-50;
    }
    
    .form-input {
        @apply block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-blue-500 focus:border-blue-500;
    }
    
    .form-label {
        @apply block text-sm font-medium text-gray-700 mb-1;
    }
    
    .form-error {
        @apply text-sm text-red-600 mt-1;
    }
    
    .form-help {
        @apply text-sm text-gray-500 mt-1;
    }
}
```

```html
<!-- Using extracted component classes -->
<div class="card">
    <div class="card-header">
        <h3 class="text-lg font-semibold text-gray-900">Card Title</h3>
    </div>
    
    <div class="card-body">
        <p class="text-gray-600">Card content goes here...</p>
        
        <form class="mt-4">
            <div class="mb-4">
                <label class="form-label" for="email">Email</label>
                <input type="email" id="email" class="form-input" placeholder="Enter your email">
                <p class="form-help">We'll never share your email.</p>
            </div>
            
            <div class="flex gap-3">
                <button type="submit" class="btn btn-primary">Submit</button>
                <button type="button" class="btn btn-secondary">Cancel</button>
            </div>
        </form>
    </div>
    
    <div class="card-footer">
        <div class="flex justify-between">
            <button class="btn btn-sm btn-secondary">Edit</button>
            <button class="btn btn-sm btn-danger">Delete</button>
        </div>
    </div>
</div>
```

### Balancing Utilities with Components
```css
/* Complex components that benefit from extraction */
@layer components {
    .navigation {
        @apply bg-white shadow-lg;
    }
    
    .navigation-container {
        @apply max-w-7xl mx-auto px-4 sm:px-6 lg:px-8;
    }
    
    .navigation-content {
        @apply flex justify-between h-16;
    }
    
    .navigation-brand {
        @apply flex items-center;
    }
    
    .navigation-menu {
        @apply hidden md:block;
    }
    
    .navigation-menu-items {
        @apply ml-10 flex items-baseline space-x-4;
    }
    
    .navigation-link {
        @apply text-gray-600 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium transition-colors duration-200;
    }
    
    .navigation-link-active {
        @apply text-blue-600 bg-blue-50;
    }
    
    .navigation-mobile-button {
        @apply md:hidden inline-flex items-center justify-center p-2 rounded-md text-gray-600 hover:text-gray-900 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-blue-500;
    }
    
    .modal-overlay {
        @apply fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50;
    }
    
    .modal-container {
        @apply relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white;
    }
    
    .modal-header {
        @apply flex items-center justify-between pb-3 border-b;
    }
    
    .modal-title {
        @apply text-lg font-semibold text-gray-900;
    }
    
    .modal-close {
        @apply text-gray-400 hover:text-gray-600 focus:outline-none focus:ring-2 focus:ring-gray-500 rounded-md p-1;
    }
    
    .modal-body {
        @apply py-4;
    }
    
    .modal-footer {
        @apply flex justify-end space-x-3 pt-3 border-t;
    }
}
```

```html
<!-- Navigation using extracted components -->
<nav class="navigation">
    <div class="navigation-container">
        <div class="navigation-content">
            <div class="navigation-brand">
                <h1 class="text-xl font-bold text-gray-800">Logo</h1>
            </div>
            
            <div class="navigation-menu">
                <div class="navigation-menu-items">
                    <a href="#" class="navigation-link navigation-link-active">Home</a>
                    <a href="#" class="navigation-link">About</a>
                    <a href="#" class="navigation-link">Contact</a>
                </div>
            </div>
            
            <button class="navigation-mobile-button">
                <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                </svg>
            </button>
        </div>
    </div>
</nav>

<!-- Modal using extracted components -->
<div class="modal-overlay">
    <div class="modal-container">
        <div class="modal-header">
            <h3 class="modal-title">Modal Title</h3>
            <button class="modal-close">
                <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                </svg>
            </button>
        </div>
        
        <div class="modal-body">
            <p class="text-gray-600">Modal content goes here...</p>
        </div>
        
        <div class="modal-footer">
            <button class="btn btn-secondary">Cancel</button>
            <button class="btn btn-primary">Confirm</button>
        </div>
    </div>
</div>
```

## Dynamic Class Management

### Conditional Class Rendering
```jsx
// React component with dynamic Tailwind classes
function Button({ variant = 'primary', size = 'md', disabled = false, children, ...props }) {
    const baseClasses = 'inline-flex items-center justify-center font-medium rounded-md focus:outline-none focus:ring-2 focus:ring-offset-2 transition-colors duration-200';
    
    const variantClasses = {
        primary: 'bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500',
        secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300 focus:ring-gray-500',
        danger: 'bg-red-600 text-white hover:bg-red-700 focus:ring-red-500',
        outline: 'border border-gray-300 bg-white text-gray-700 hover:bg-gray-50 focus:ring-blue-500'
    };
    
    const sizeClasses = {
        sm: 'px-3 py-1.5 text-xs',
        md: 'px-4 py-2 text-sm',
        lg: 'px-6 py-3 text-base'
    };
    
    const disabledClasses = disabled ? 'opacity-50 cursor-not-allowed' : '';
    
    const className = [
        baseClasses,
        variantClasses[variant],
        sizeClasses[size],
        disabledClasses
    ].filter(Boolean).join(' ');
    
    return (
        <button
            className={className}
            disabled={disabled}
            {...props}
        >
            {children}
        </button>
    );
}

// Usage
function App() {
    const [loading, setLoading] = useState(false);
    
    return (
        <div className="p-6 space-y-4">
            <Button variant="primary" size="lg">
                Primary Button
            </Button>
            
            <Button variant="secondary" size="md">
                Secondary Button
            </Button>
            
            <Button variant="danger" size="sm" disabled={loading}>
                {loading ? 'Loading...' : 'Delete'}
            </Button>
            
            <Button variant="outline" size="md">
                Outline Button
            </Button>
        </div>
    );
}
```

### Class Names Utility Libraries
```jsx
// Using clsx for conditional classes
import clsx from 'clsx';

function Card({ variant = 'default', size = 'md', hover = true, children }) {
    const className = clsx(
        // Base classes
        'bg-white rounded-lg shadow-md overflow-hidden',
        
        // Variant classes
        {
            'border border-gray-200': variant === 'default',
            'border-2 border-blue-200 bg-blue-50': variant === 'highlighted',
            'border-2 border-red-200 bg-red-50': variant === 'error',
            'border-2 border-green-200 bg-green-50': variant === 'success'
        },
        
        // Size classes
        {
            'p-4': size === 'sm',
            'p-6': size === 'md',
            'p-8': size === 'lg'
        },
        
        // Conditional classes
        {
            'hover:shadow-lg transition-shadow duration-200': hover
        }
    );
    
    return (
        <div className={className}>
            {children}
        </div>
    );
}

// Using classnames with Tailwind
import classNames from 'classnames';

function Alert({ type = 'info', dismissible = false, children, onDismiss }) {
    const className = classNames(
        'px-4 py-3 rounded-md border',
        {
            'bg-blue-50 border-blue-200 text-blue-800': type === 'info',
            'bg-green-50 border-green-200 text-green-800': type === 'success',
            'bg-yellow-50 border-yellow-200 text-yellow-800': type === 'warning',
            'bg-red-50 border-red-200 text-red-800': type === 'error'
        },
        {
            'pr-10': dismissible
        }
    );
    
    return (
        <div className={className} role="alert">
            {children}
            {dismissible && (
                <button
                    onClick={onDismiss}
                    className="absolute top-0 right-0 p-2 text-current hover:opacity-75"
                >
                    <svg className="h-4 w-4" fill="currentColor" viewBox="0 0 20 20">
                        <path fillRule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clipRule="evenodd" />
                    </svg>
                </button>
            )}
        </div>
    );
}
```

### State-Dependent Styling
```jsx
// Component with state-dependent styling
function ToggleButton({ isActive, onToggle, children }) {
    const className = clsx(
        'px-4 py-2 rounded-md font-medium transition-all duration-200',
        {
            'bg-blue-600 text-white shadow-md': isActive,
            'bg-gray-200 text-gray-700 hover:bg-gray-300': !isActive
        }
    );
    
    return (
        <button
            className={className}
            onClick={onToggle}
        >
            {children}
        </button>
    );
}

// Form input with validation states
function FormInput({ 
    label, 
    type = 'text', 
    value, 
    onChange, 
    error, 
    helpText, 
    required = false 
}) {
    const inputClassName = clsx(
        'block w-full px-3 py-2 border rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-offset-2 transition-colors duration-200',
        {
            'border-gray-300 focus:ring-blue-500 focus:border-blue-500': !error,
            'border-red-300 focus:ring-red-500 focus:border-red-500': error
        }
    );
    
    return (
        <div className="space-y-1">
            <label className="block text-sm font-medium text-gray-700">
                {label}
                {required && <span className="text-red-500 ml-1">*</span>}
            </label>
            
            <input
                type={type}
                value={value}
                onChange={onChange}
                className={inputClassName}
            />
            
            {error && (
                <p className="text-sm text-red-600">{error}</p>
            )}
            
            {helpText && !error && (
                <p className="text-sm text-gray-500">{helpText}</p>
            )}
        </div>
    );
}

// Progress bar with dynamic styling
function ProgressBar({ progress = 0, size = 'md', color = 'blue' }) {
    const sizeClasses = {
        sm: 'h-2',
        md: 'h-3',
        lg: 'h-4'
    };
    
    const colorClasses = {
        blue: 'bg-blue-600',
        green: 'bg-green-600',
        red: 'bg-red-600',
        yellow: 'bg-yellow-600'
    };
    
    return (
        <div className={clsx('w-full bg-gray-200 rounded-full overflow-hidden', sizeClasses[size])}>
            <div
                className={clsx('h-full transition-all duration-300 ease-out', colorClasses[color])}
                style={{ width: `${Math.min(100, Math.max(0, progress))}%` }}
            />
        </div>
    );
}
```

## Advanced Tailwind Features

### Custom Configuration
```javascript
// tailwind.config.js
module.exports = {
    content: [
        './src/**/*.{js,jsx,ts,tsx}',
        './public/index.html'
    ],
    theme: {
        extend: {
            colors: {
                primary: {
                    50: '#eff6ff',
                    100: '#dbeafe',
                    200: '#bfdbfe',
                    300: '#93c5fd',
                    400: '#60a5fa',
                    500: '#3b82f6',
                    600: '#2563eb',
                    700: '#1d4ed8',
                    800: '#1e40af',
                    900: '#1e3a8a',
                },
                secondary: {
                    50: '#f8fafc',
                    100: '#f1f5f9',
                    200: '#e2e8f0',
                    300: '#cbd5e1',
                    400: '#94a3b8',
                    500: '#64748b',
                    600: '#475569',
                    700: '#334155',
                    800: '#1e293b',
                    900: '#0f172a',
                }
            },
            fontFamily: {
                sans: ['Inter', 'system-ui', 'sans-serif'],
                mono: ['Fira Code', 'monospace']
            },
            spacing: {
                '18': '4.5rem',
                '88': '22rem',
                '128': '32rem'
            },
            borderRadius: {
                '4xl': '2rem'
            },
            boxShadow: {
                'soft': '0 2px 15px -3px rgba(0, 0, 0, 0.07), 0 10px 20px -2px rgba(0, 0, 0, 0.04)',
                'glow': '0 0 20px rgba(59, 130, 246, 0.5)'
            }
        }
    },
    plugins: [
        require('@tailwindcss/forms'),
        require('@tailwindcss/typography'),
        require('@tailwindcss/aspect-ratio'),
        // Custom plugin
        function({ addUtilities, theme }) {
            const newUtilities = {
                '.text-shadow': {
                    textShadow: '0 2px 4px rgba(0,0,0,0.10)'
                },
                '.text-shadow-md': {
                    textShadow: '0 4px 8px rgba(0,0,0,0.12), 0 2px 4px rgba(0,0,0,0.08)'
                },
                '.text-shadow-lg': {
                    textShadow: '0 15px 35px rgba(0,0,0,0.12), 0 5px 15px rgba(0,0,0,0.07)'
                }
            };
            
            addUtilities(newUtilities);
        }
    ]
};
```

### Dark Mode Implementation
```css
/* styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
    :root {
        --color-primary: 59 130 246;
        --color-secondary: 100 116 139;
    }
    
    .dark {
        --color-primary: 96 165 250;
        --color-secondary: 148 163 184;
    }
}

@layer components {
    .theme-toggle {
        @apply p-2 rounded-md bg-gray-100 dark:bg-gray-800 text-gray-600 dark:text-gray-300 hover:bg-gray-200 dark:hover:bg-gray-700 transition-colors duration-200;
    }
    
    .card-dark {
        @apply bg-white dark:bg-gray-800 border-gray-200 dark:border-gray-700 text-gray-900 dark:text-gray-100;
    }
    
    .input-dark {
        @apply bg-white dark:bg-gray-800 border-gray-300 dark:border-gray-600 text-gray-900 dark:text-gray-100 placeholder-gray-500 dark:placeholder-gray-400 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-blue-500 dark:focus:border-blue-400;
    }
}
```

```html
<!-- Dark mode toggle component -->
<button 
    id="theme-toggle"
    class="theme-toggle"
    onclick="toggleTheme()"
>
    <svg id="theme-icon" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
    </svg>
</button>

<!-- Dark mode aware components -->
<div class="card-dark rounded-lg shadow-md p-6">
    <h2 class="text-xl font-semibold mb-4">Dark Mode Card</h2>
    <p class="text-gray-600 dark:text-gray-300 mb-4">
        This card adapts to light and dark themes.
    </p>
    <input 
        type="text" 
        class="input-dark w-full px-3 py-2 border rounded-md"
        placeholder="Dark mode input"
    />
</div>

<script>
function toggleTheme() {
    const html = document.documentElement;
    const themeIcon = document.getElementById('theme-icon');
    
    if (html.classList.contains('dark')) {
        html.classList.remove('dark');
        localStorage.setItem('theme', 'light');
        themeIcon.innerHTML = '<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />';
    } else {
        html.classList.add('dark');
        localStorage.setItem('theme', 'dark');
        themeIcon.innerHTML = '<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />';
    }
}

// Initialize theme
if (localStorage.getItem('theme') === 'dark' || (!localStorage.getItem('theme') && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    document.documentElement.classList.add('dark');
}
</script>
```

## Exercise: Component Library with Tailwind

Create a component library using Tailwind CSS:
- Extract reusable component classes with @apply
- Implement dynamic class management
- Create state-dependent styling patterns
- Add dark mode support
- Use custom configuration and plugins
- Build responsive components

## Key Takeaways
- Use @apply directive for complex, reusable components
- Balance utility classes with component extraction
- Implement dynamic class management for interactive components
- Use utility libraries like clsx for conditional classes
- Create state-dependent styling patterns
- Implement dark mode with Tailwind's dark mode features
- Use custom configuration for brand-specific design tokens
- Create custom utilities and plugins when needed
- Maintain Tailwind's benefits while reducing repetition
- Test components across different states and themes
