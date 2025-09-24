# CSS Architecture Patterns

## Introduction
CSS architecture patterns help organize and maintain large-scale stylesheets. This lesson covers methodologies like OOCSS, SMACSS, Atomic CSS, and modern approaches for scalable CSS.

## Object-Oriented CSS (OOCSS)

### Separation of Structure and Skin
```css
/* Structure - layout and positioning */
.structure {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    padding: 1rem;
    margin: 0 auto;
    max-width: 1200px;
}

.structure-horizontal {
    flex-direction: row;
    align-items: center;
}

.structure-centered {
    justify-content: center;
    align-items: center;
}

/* Skin - visual appearance */
.skin-primary {
    background-color: #007bff;
    color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.skin-secondary {
    background-color: #6c757d;
    color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.skin-outline {
    background-color: transparent;
    color: #007bff;
    border: 2px solid #007bff;
    border-radius: 8px;
}

/* Combined classes */
.button {
    /* Structure */
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    font-weight: 500;
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
}

.button-primary {
    /* Skin */
    background-color: #007bff;
    color: white;
    border-radius: 8px;
}

.button-secondary {
    background-color: #6c757d;
    color: white;
    border-radius: 8px;
}

.button-outline {
    background-color: transparent;
    color: #007bff;
    border: 2px solid #007bff;
    border-radius: 8px;
}
```

### Content and Container Separation
```css
/* Container - layout and positioning */
.container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 1rem;
}

.container-fluid {
    width: 100%;
    padding: 0 1rem;
}

.container-sm {
    max-width: 540px;
}

.container-md {
    max-width: 720px;
}

.container-lg {
    max-width: 960px;
}

.container-xl {
    max-width: 1140px;
}

/* Content - styling and appearance */
.content {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
}

.content-primary {
    background: #f8f9fa;
    border: 1px solid #dee2e6;
}

.content-secondary {
    background: #e9ecef;
    border: 1px solid #ced4da;
}

/* Grid system */
.grid {
    display: grid;
    gap: 1rem;
}

.grid-2 {
    grid-template-columns: repeat(2, 1fr);
}

.grid-3 {
    grid-template-columns: repeat(3, 1fr);
}

.grid-4 {
    grid-template-columns: repeat(4, 1fr);
}

.grid-auto {
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
}
```

## SMACSS (Scalable and Modular Architecture)

### Base Rules
```css
/* Base styles - HTML elements */
* {
    box-sizing: border-box;
}

html {
    font-size: 16px;
    line-height: 1.5;
}

body {
    font-family: 'Inter', sans-serif;
    color: #333;
    background-color: #fff;
    margin: 0;
    padding: 0;
}

h1, h2, h3, h4, h5, h6 {
    margin: 0 0 1rem 0;
    font-weight: 600;
    line-height: 1.2;
}

h1 { font-size: 2.5rem; }
h2 { font-size: 2rem; }
h3 { font-size: 1.75rem; }
h4 { font-size: 1.5rem; }
h5 { font-size: 1.25rem; }
h6 { font-size: 1rem; }

p {
    margin: 0 0 1rem 0;
    line-height: 1.6;
}

a {
    color: #007bff;
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}

img {
    max-width: 100%;
    height: auto;
}

button {
    font-family: inherit;
    font-size: inherit;
    line-height: inherit;
}

input, textarea, select {
    font-family: inherit;
    font-size: inherit;
    line-height: inherit;
}
```

### Layout Rules
```css
/* Layout - major structural components */
.l-header {
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    position: sticky;
    top: 0;
    z-index: 1000;
}

.l-main {
    min-height: calc(100vh - 200px);
    padding: 2rem 0;
}

.l-footer {
    background: #f8f9fa;
    padding: 2rem 0;
    border-top: 1px solid #dee2e6;
}

.l-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 1rem;
}

.l-sidebar {
    width: 250px;
    background: #f8f9fa;
    padding: 1rem;
    border-right: 1px solid #dee2e6;
}

.l-content {
    flex: 1;
    padding: 1rem;
}

.l-grid {
    display: grid;
    grid-template-columns: 250px 1fr;
    gap: 0;
    min-height: 100vh;
}

@media (max-width: 768px) {
    .l-grid {
        grid-template-columns: 1fr;
    }
    
    .l-sidebar {
        width: 100%;
        border-right: none;
        border-bottom: 1px solid #dee2e6;
    }
}
```

### Module Rules
```css
/* Module - reusable components */
.m-card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
}

.m-card-header {
    padding: 1.5rem;
    border-bottom: 1px solid #dee2e6;
    background: #f8f9fa;
}

.m-card-body {
    padding: 1.5rem;
}

.m-card-footer {
    padding: 1.5rem;
    border-top: 1px solid #dee2e6;
    background: #f8f9fa;
}

.m-button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    font-size: 1rem;
    font-weight: 500;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.m-button--primary {
    background: #007bff;
    color: white;
}

.m-button--secondary {
    background: #6c757d;
    color: white;
}

.m-button--large {
    padding: 0.75rem 1.5rem;
    font-size: 1.125rem;
}

.m-button--small {
    padding: 0.25rem 0.75rem;
    font-size: 0.875rem;
}

.m-form-group {
    margin-bottom: 1.5rem;
}

.m-form-label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 500;
    color: #333;
}

.m-form-input {
    width: 100%;
    padding: 0.75rem;
    font-size: 1rem;
    border: 2px solid #dee2e6;
    border-radius: 8px;
    background: white;
    transition: border-color 0.3s ease;
}

.m-form-input:focus {
    border-color: #007bff;
    outline: none;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.25);
}
```

### State Rules
```css
/* State - temporary states */
.is-hidden {
    display: none !important;
}

.is-visible {
    display: block !important;
}

.is-active {
    background: #007bff !important;
    color: white !important;
}

.is-disabled {
    opacity: 0.6;
    cursor: not-allowed;
    pointer-events: none;
}

.is-loading {
    position: relative;
    color: transparent;
}

.is-loading::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 20px;
    height: 20px;
    margin: -10px 0 0 -10px;
    border: 2px solid #f3f3f3;
    border-top: 2px solid #007bff;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

.is-error {
    border-color: #dc3545 !important;
    box-shadow: 0 0 0 3px rgba(220, 53, 69, 0.25) !important;
}

.is-success {
    border-color: #28a745 !important;
    box-shadow: 0 0 0 3px rgba(40, 167, 69, 0.25) !important;
}
```

## Atomic CSS Approach

### Utility Classes
```css
/* Spacing utilities */
.p-0 { padding: 0; }
.p-1 { padding: 0.25rem; }
.p-2 { padding: 0.5rem; }
.p-3 { padding: 0.75rem; }
.p-4 { padding: 1rem; }
.p-5 { padding: 1.25rem; }
.p-6 { padding: 1.5rem; }

.m-0 { margin: 0; }
.m-1 { margin: 0.25rem; }
.m-2 { margin: 0.5rem; }
.m-3 { margin: 0.75rem; }
.m-4 { margin: 1rem; }
.m-5 { margin: 1.25rem; }
.m-6 { margin: 1.5rem; }

/* Typography utilities */
.text-xs { font-size: 0.75rem; }
.text-sm { font-size: 0.875rem; }
.text-base { font-size: 1rem; }
.text-lg { font-size: 1.125rem; }
.text-xl { font-size: 1.25rem; }
.text-2xl { font-size: 1.5rem; }

.font-thin { font-weight: 100; }
.font-light { font-weight: 300; }
.font-normal { font-weight: 400; }
.font-medium { font-weight: 500; }
.font-semibold { font-weight: 600; }
.font-bold { font-weight: 700; }

.text-left { text-align: left; }
.text-center { text-align: center; }
.text-right { text-align: right; }

/* Color utilities */
.text-primary { color: #007bff; }
.text-secondary { color: #6c757d; }
.text-success { color: #28a745; }
.text-danger { color: #dc3545; }
.text-warning { color: #ffc107; }
.text-info { color: #17a2b8; }

.bg-primary { background-color: #007bff; }
.bg-secondary { background-color: #6c757d; }
.bg-success { background-color: #28a745; }
.bg-danger { background-color: #dc3545; }
.bg-warning { background-color: #ffc107; }
.bg-info { background-color: #17a2b8; }

/* Layout utilities */
.d-block { display: block; }
.d-inline { display: inline; }
.d-inline-block { display: inline-block; }
.d-flex { display: flex; }
.d-grid { display: grid; }
.d-none { display: none; }

.flex-row { flex-direction: row; }
.flex-column { flex-direction: column; }
.flex-wrap { flex-wrap: wrap; }
.flex-nowrap { flex-wrap: nowrap; }

.justify-start { justify-content: flex-start; }
.justify-center { justify-content: center; }
.justify-end { justify-content: flex-end; }
.justify-between { justify-content: space-between; }
.justify-around { justify-content: space-around; }

.items-start { align-items: flex-start; }
.items-center { align-items: center; }
.items-end { align-items: flex-end; }
.items-stretch { align-items: stretch; }

/* Border utilities */
.border { border: 1px solid #dee2e6; }
.border-top { border-top: 1px solid #dee2e6; }
.border-right { border-right: 1px solid #dee2e6; }
.border-bottom { border-bottom: 1px solid #dee2e6; }
.border-left { border-left: 1px solid #dee2e6; }

.rounded { border-radius: 0.25rem; }
.rounded-sm { border-radius: 0.125rem; }
.rounded-lg { border-radius: 0.5rem; }
.rounded-xl { border-radius: 0.75rem; }
.rounded-full { border-radius: 9999px; }

/* Shadow utilities */
.shadow-sm { box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); }
.shadow { box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1), 0 1px 2px rgba(0, 0, 0, 0.06); }
.shadow-md { box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1), 0 2px 4px rgba(0, 0, 0, 0.06); }
.shadow-lg { box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1), 0 4px 6px rgba(0, 0, 0, 0.05); }
.shadow-xl { box-shadow: 0 20px 25px rgba(0, 0, 0, 0.1), 0 10px 10px rgba(0, 0, 0, 0.04); }
```

## Modern CSS Architecture

### Component-Based Architecture
```css
/* Component structure */
.component {
    /* Component base styles */
    --component-bg: white;
    --component-padding: 1rem;
    --component-radius: 8px;
    --component-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    
    background: var(--component-bg);
    padding: var(--component-padding);
    border-radius: var(--component-radius);
    box-shadow: var(--component-shadow);
}

/* Component variants */
.component--primary {
    --component-bg: #007bff;
    --component-shadow: 0 4px 8px rgba(0, 123, 255, 0.2);
}

.component--secondary {
    --component-bg: #6c757d;
    --component-shadow: 0 4px 8px rgba(108, 117, 125, 0.2);
}

.component--large {
    --component-padding: 2rem;
    --component-radius: 12px;
}

.component--small {
    --component-padding: 0.5rem;
    --component-radius: 4px;
}

/* Component states */
.component.is-active {
    --component-bg: #28a745;
    --component-shadow: 0 4px 8px rgba(40, 167, 69, 0.2);
}

.component.is-disabled {
    opacity: 0.6;
    cursor: not-allowed;
    pointer-events: none;
}

/* Component elements */
.component__header {
    margin-bottom: 1rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid #dee2e6;
}

.component__body {
    margin-bottom: 1rem;
}

.component__footer {
    margin-top: 1rem;
    padding-top: 1rem;
    border-top: 1px solid #dee2e6;
}

.component__title {
    font-size: 1.25rem;
    font-weight: 600;
    margin: 0 0 0.5rem 0;
}

.component__description {
    color: #6c757d;
    margin: 0;
}
```

### CSS-in-JS Integration
```css
/* CSS custom properties for JavaScript integration */
:root {
    --theme-primary: #007bff;
    --theme-secondary: #6c757d;
    --theme-success: #28a745;
    --theme-danger: #dc3545;
    --theme-warning: #ffc107;
    --theme-info: #17a2b8;
    
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 3rem;
    
    --font-size-xs: 0.75rem;
    --font-size-sm: 0.875rem;
    --font-size-base: 1rem;
    --font-size-lg: 1.125rem;
    --font-size-xl: 1.25rem;
    
    --border-radius-sm: 0.125rem;
    --border-radius-md: 0.375rem;
    --border-radius-lg: 0.5rem;
    --border-radius-xl: 1rem;
}

/* Dynamic component styling */
.dynamic-component {
    background: var(--component-bg, var(--theme-primary));
    color: var(--component-color, white);
    padding: var(--component-padding, var(--spacing-md));
    border-radius: var(--component-radius, var(--border-radius-md));
    font-size: var(--component-font-size, var(--font-size-base));
}
```

## Exercise: CSS Architecture System

Create a CSS architecture system with:
- OOCSS structure and skin separation
- SMACSS base, layout, module, and state rules
- Atomic CSS utility classes
- Component-based architecture
- CSS custom properties integration
- Responsive design patterns

## Key Takeaways
- OOCSS separates structure from skin for reusability
- SMACSS provides a scalable architecture with clear categories
- Atomic CSS uses utility classes for rapid development
- Component-based architecture promotes modularity
- CSS custom properties enable dynamic theming
- Choose architecture patterns based on project needs
- Document your CSS architecture for team consistency
- Use naming conventions that reflect your chosen methodology
- Test architecture patterns with real projects
- Evolve your architecture as projects grow
