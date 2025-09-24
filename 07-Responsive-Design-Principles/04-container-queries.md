# Container Queries

## Introduction
Container queries are a powerful CSS feature that allows components to respond to their container's size rather than the viewport size. This lesson covers container query syntax, use cases, and implementation patterns.

## Container Query Basics

### Setting Up Container Queries
```css
/* Container setup */
.card-container {
    container-type: inline-size;
    width: 100%;
}

/* Basic container query */
@container (min-width: 300px) {
    .card {
        display: flex;
        flex-direction: row;
        align-items: center;
    }
    
    .card-image {
        width: 100px;
        height: 100px;
        margin-right: 1rem;
    }
}

@container (min-width: 500px) {
    .card {
        padding: 2rem;
    }
    
    .card-image {
        width: 150px;
        height: 150px;
        margin-right: 2rem;
    }
}

@container (min-width: 700px) {
    .card {
        flex-direction: column;
        text-align: center;
    }
    
    .card-image {
        width: 200px;
        height: 200px;
        margin-right: 0;
        margin-bottom: 1rem;
    }
}
```

### Container Types
```css
/* Inline-size container (width-based) */
.inline-container {
    container-type: inline-size;
    width: 100%;
}

/* Block-size container (height-based) */
.block-container {
    container-type: block-size;
    height: 100%;
}

/* Both dimensions */
.size-container {
    container-type: size;
    width: 100%;
    height: 100%;
}

/* Container queries for different types */
@container inline-size (min-width: 300px) {
    .inline-responsive {
        display: flex;
        flex-direction: row;
    }
}

@container block-size (min-height: 200px) {
    .block-responsive {
        padding: 2rem;
    }
}

@container size (min-width: 300px) and (min-height: 200px) {
    .size-responsive {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 1rem;
    }
}
```

## Container Query Units

### Container Query Units (cqw, cqh, cqi, cqb)
```css
/* Container setup */
.container {
    container-type: inline-size;
    width: 100%;
}

/* Container query units */
.container-child {
    /* Container query width - 50% of container width */
    width: 50cqw;
    
    /* Container query height - 30% of container height */
    height: 30cqh;
    
    /* Container query inline size - 2% of container inline size */
    font-size: 2cqi;
    
    /* Container query block size - 1% of container block size */
    padding: 1cqb;
    
    /* Responsive padding using container units */
    padding: 2cqw 1cqh;
    
    /* Responsive margin */
    margin: 0.5cqw;
}

/* Responsive typography with container units */
.container-text {
    font-size: clamp(1rem, 2cqi, 2rem);
    line-height: clamp(1.4, 1.2 + 0.5cqi, 1.6);
}

/* Responsive spacing */
.container-spacing {
    padding: clamp(1rem, 3cqw, 3rem);
    margin: clamp(0.5rem, 2cqw, 2rem);
    gap: clamp(1rem, 2cqw, 2rem);
}
```

### Advanced Container Units
```css
/* Complex container setup */
.complex-container {
    container-type: size;
    width: 100%;
    height: 100%;
}

/* Advanced container unit usage */
.complex-child {
    /* Responsive grid with container units */
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(20cqw, 1fr));
    gap: 2cqw;
    
    /* Responsive padding */
    padding: 3cqw 2cqh;
    
    /* Responsive border radius */
    border-radius: 1cqw;
    
    /* Responsive font size */
    font-size: clamp(0.875rem, 1.5cqi, 1.5rem);
}

/* Responsive shadows */
.container-shadow {
    box-shadow: 
        0 1cqw 2cqw rgba(0, 0, 0, 0.1),
        0 0.5cqw 1cqw rgba(0, 0, 0, 0.05);
}
```

## Real-World Container Query Examples

### Responsive Card Component
```css
/* Card container */
.card-container {
    container-type: inline-size;
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
}

.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    transition: all 0.3s ease;
}

.card-image {
    width: 100%;
    height: 200px;
    object-fit: cover;
}

.card-content {
    padding: 1rem;
}

.card-title {
    font-size: 1.25rem;
    font-weight: 600;
    margin: 0 0 0.5rem 0;
}

.card-description {
    color: #666;
    line-height: 1.5;
    margin: 0 0 1rem 0;
}

.card-actions {
    display: flex;
    gap: 0.5rem;
}

/* Container queries for different card sizes */
@container (min-width: 300px) {
    .card {
        display: flex;
        flex-direction: row;
    }
    
    .card-image {
        width: 150px;
        height: 150px;
        flex-shrink: 0;
    }
    
    .card-content {
        flex: 1;
        padding: 1.5rem;
    }
}

@container (min-width: 500px) {
    .card-image {
        width: 200px;
        height: 200px;
    }
    
    .card-content {
        padding: 2rem;
    }
    
    .card-title {
        font-size: 1.5rem;
    }
    
    .card-actions {
        justify-content: flex-end;
    }
}

@container (min-width: 700px) {
    .card {
        flex-direction: column;
    }
    
    .card-image {
        width: 100%;
        height: 300px;
    }
    
    .card-content {
        padding: 2.5rem;
        text-align: center;
    }
    
    .card-title {
        font-size: 2rem;
    }
    
    .card-actions {
        justify-content: center;
    }
}
```

### Responsive Navigation Component
```css
/* Navigation container */
.nav-container {
    container-type: inline-size;
    width: 100%;
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.nav-content {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1rem;
}

.nav-brand {
    font-size: 1.25rem;
    font-weight: bold;
    color: #333;
}

.nav-menu {
    display: none;
}

.nav-toggle {
    display: block;
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    padding: 0.5rem;
}

.nav-menu-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
}

.nav-menu-item {
    margin: 0;
}

.nav-menu-link {
    display: block;
    padding: 0.75rem;
    color: #333;
    text-decoration: none;
    border-radius: 4px;
    transition: background-color 0.3s ease;
}

.nav-menu-link:hover {
    background: #f8f9fa;
}

/* Container queries for responsive navigation */
@container (min-width: 400px) {
    .nav-content {
        padding: 1.5rem;
    }
    
    .nav-brand {
        font-size: 1.5rem;
    }
}

@container (min-width: 600px) {
    .nav-toggle {
        display: none;
    }
    
    .nav-menu {
        display: block;
    }
    
    .nav-menu-list {
        flex-direction: row;
        gap: 1rem;
    }
    
    .nav-menu-link {
        padding: 0.5rem 1rem;
    }
}

@container (min-width: 800px) {
    .nav-content {
        padding: 2rem;
        max-width: 1200px;
        margin: 0 auto;
    }
    
    .nav-menu-list {
        gap: 2rem;
    }
}
```

### Responsive Form Component
```css
/* Form container */
.form-container {
    container-type: inline-size;
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
}

.form {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
}

.form-group {
    margin-bottom: 1.5rem;
}

.form-label {
    display: block;
    margin-bottom: 0.5rem;
    font-weight: 500;
    color: #333;
}

.form-input {
    width: 100%;
    padding: 12px 16px;
    font-size: 16px;
    border: 2px solid #dee2e6;
    border-radius: 8px;
    background: white;
    transition: border-color 0.3s ease;
}

.form-input:focus {
    border-color: #007bff;
    outline: none;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.25);
}

.form-row {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.form-button {
    width: 100%;
    padding: 12px 24px;
    font-size: 16px;
    font-weight: 500;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.form-button-primary {
    background: #007bff;
    color: white;
}

.form-button-primary:hover {
    background: #0056b3;
}

/* Container queries for responsive forms */
@container (min-width: 400px) {
    .form {
        padding: 1.5rem;
    }
    
    .form-group {
        margin-bottom: 2rem;
    }
}

@container (min-width: 500px) {
    .form-row {
        flex-direction: row;
    }
    
    .form-row .form-group {
        flex: 1;
    }
    
    .form-button {
        width: auto;
        min-width: 120px;
    }
}

@container (min-width: 600px) {
    .form {
        padding: 2rem;
    }
    
    .form-group {
        margin-bottom: 2.5rem;
    }
    
    .form-input {
        padding: 16px 20px;
    }
    
    .form-button {
        padding: 16px 32px;
    }
}
```

## Container Query Patterns

### Component Library Pattern
```css
/* Button component with container queries */
.button-container {
    container-type: inline-size;
    display: inline-block;
}

.button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    font-size: 0.875rem;
    font-weight: 500;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.button-primary {
    background: #007bff;
    color: white;
}

.button-secondary {
    background: #6c757d;
    color: white;
}

/* Container queries for button sizing */
@container (min-width: 100px) {
    .button {
        padding: 0.75rem 1.5rem;
        font-size: 1rem;
    }
}

@container (min-width: 150px) {
    .button {
        padding: 1rem 2rem;
        font-size: 1.125rem;
        border-radius: 8px;
    }
}

@container (min-width: 200px) {
    .button {
        padding: 1.25rem 2.5rem;
        font-size: 1.25rem;
        border-radius: 12px;
    }
}
```

### Grid Layout Pattern
```css
/* Grid container */
.grid-container {
    container-type: inline-size;
    width: 100%;
}

.grid {
    display: grid;
    gap: 1rem;
    padding: 1rem;
}

.grid-item {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
}

/* Container queries for responsive grid */
@container (min-width: 300px) {
    .grid {
        grid-template-columns: repeat(2, 1fr);
        gap: 1.5rem;
        padding: 1.5rem;
    }
}

@container (min-width: 500px) {
    .grid {
        grid-template-columns: repeat(3, 1fr);
        gap: 2rem;
        padding: 2rem;
    }
}

@container (min-width: 700px) {
    .grid {
        grid-template-columns: repeat(4, 1fr);
    }
}

@container (min-width: 900px) {
    .grid {
        grid-template-columns: repeat(5, 1fr);
    }
}
```

## Exercise: Container Query Components

Create responsive components using container queries:
- Responsive card component that adapts to container size
- Navigation component with container-based breakpoints
- Form component with responsive layouts
- Button component with size variations
- Grid layout that responds to container dimensions

## Key Takeaways
- Container queries allow components to respond to their container's size
- Use container-type to define what dimensions to track
- Container query units (cqw, cqh, cqi, cqb) provide responsive sizing
- Container queries work alongside media queries for comprehensive responsive design
- Test container queries across different container sizes
- Use container queries for component-level responsiveness
- Combine container queries with CSS Grid and Flexbox for powerful layouts
- Container queries are perfect for reusable component libraries
- Consider fallbacks for browsers that don't support container queries
- Use container queries to create truly modular, responsive components
