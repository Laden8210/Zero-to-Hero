# Mobile-First Development

## Introduction
Mobile-first development is a design approach that starts with mobile devices and progressively enhances for larger screens. This lesson covers mobile-first principles, progressive enhancement, and responsive patterns.

## Mobile-First Principles

### Core Philosophy
```css
/* Mobile-first approach - start with mobile styles */
.container {
    /* Base styles for mobile */
    width: 100%;
    padding: 1rem;
    margin: 0 auto;
    background: #f8f9fa;
}

.navigation {
    /* Mobile navigation */
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    padding: 1rem;
}

.navigation-item {
    padding: 0.75rem;
    background: white;
    border-radius: 4px;
    text-align: center;
}

/* Progressive enhancement for larger screens */
@media (min-width: 576px) {
    .container {
        max-width: 540px;
        padding: 1.5rem;
    }
    
    .navigation {
        flex-direction: row;
        justify-content: space-between;
        padding: 1.5rem;
    }
    
    .navigation-item {
        flex: 1;
        margin: 0 0.25rem;
    }
}

@media (min-width: 768px) {
    .container {
        max-width: 720px;
        padding: 2rem;
    }
    
    .navigation {
        padding: 2rem;
    }
}

@media (min-width: 992px) {
    .container {
        max-width: 960px;
    }
}

@media (min-width: 1200px) {
    .container {
        max-width: 1140px;
    }
}
```

### Touch-First Design
```css
/* Touch-friendly sizing */
.touch-target {
    min-height: 44px;  /* iOS minimum touch target */
    min-width: 44px;
    padding: 12px 16px;
    margin: 4px;
}

/* Touch-friendly buttons */
.button {
    min-height: 44px;
    padding: 12px 24px;
    font-size: 16px; /* Prevents zoom on iOS */
    border: none;
    border-radius: 8px;
    cursor: pointer;
    touch-action: manipulation; /* Prevents double-tap zoom */
}

.button-primary {
    background: #007bff;
    color: white;
}

.button-secondary {
    background: #6c757d;
    color: white;
}

/* Touch-friendly form inputs */
.input {
    min-height: 44px;
    padding: 12px 16px;
    font-size: 16px; /* Prevents zoom on iOS */
    border: 2px solid #dee2e6;
    border-radius: 8px;
    width: 100%;
}

.input:focus {
    border-color: #007bff;
    outline: none;
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.25);
}

/* Touch-friendly checkboxes and radio buttons */
.checkbox,
.radio {
    min-width: 44px;
    min-height: 44px;
    margin: 0;
    cursor: pointer;
}

.checkbox-label,
.radio-label {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 8px;
    cursor: pointer;
}
```

## Progressive Enhancement

### Content-First Approach
```html
<!-- Mobile-first HTML structure -->
<article class="card">
    <header class="card-header">
        <h2 class="card-title">Article Title</h2>
        <time class="card-date" datetime="2024-01-15">January 15, 2024</time>
    </header>
    
    <div class="card-content">
        <p class="card-excerpt">Article excerpt goes here...</p>
        <div class="card-meta">
            <span class="card-author">By John Doe</span>
            <span class="card-category">Technology</span>
        </div>
    </div>
    
    <footer class="card-footer">
        <button class="button button-primary">Read More</button>
        <div class="card-actions">
            <button class="button-icon" aria-label="Share">
                <svg class="icon" viewBox="0 0 24 24">
                    <path d="M18 16.08c-.76 0-1.44.3-1.96.77L8.91 12.7c.05-.23.09-.46.09-.7s-.04-.47-.09-.7l7.05-4.11c.54.5 1.25.81 2.04.81 1.66 0 3-1.34 3-3s-1.34-3-3-3-3 1.34-3 3c0 .24.04.47.09.7L8.04 9.81C7.5 9.31 6.79 9 6 9c-1.66 0-3 1.34-3 3s1.34 3 3 3c.79 0 1.5-.31 2.04-.81l7.12 4.16c-.05.21-.08.43-.08.65 0 1.61 1.31 2.92 2.92 2.92s2.92-1.31 2.92-2.92-1.31-2.92-2.92-2.92z"/>
                </svg>
            </button>
            <button class="button-icon" aria-label="Bookmark">
                <svg class="icon" viewBox="0 0 24 24">
                    <path d="M17 3H7c-1.1 0-1.99.9-1.99 2L5 21l7-3 7 3V5c0-1.1-.9-2-2-2z"/>
                </svg>
            </button>
        </div>
    </footer>
</article>
```

```css
/* Mobile-first card styles */
.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    margin-bottom: 1rem;
}

.card-header {
    padding: 1rem;
    border-bottom: 1px solid #e9ecef;
}

.card-title {
    font-size: 1.25rem;
    font-weight: 600;
    margin: 0 0 0.5rem 0;
    line-height: 1.3;
}

.card-date {
    font-size: 0.875rem;
    color: #6c757d;
}

.card-content {
    padding: 1rem;
}

.card-excerpt {
    margin: 0 0 1rem 0;
    line-height: 1.6;
    color: #495057;
}

.card-meta {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    font-size: 0.875rem;
    color: #6c757d;
}

.card-footer {
    padding: 1rem;
    border-top: 1px solid #e9ecef;
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.card-actions {
    display: flex;
    gap: 0.5rem;
    justify-content: center;
}

.button-icon {
    width: 44px;
    height: 44px;
    border: none;
    background: #f8f9fa;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
}

.icon {
    width: 20px;
    height: 20px;
    fill: currentColor;
}

/* Progressive enhancement for larger screens */
@media (min-width: 768px) {
    .card {
        display: flex;
        flex-direction: row;
        max-width: 800px;
        margin: 0 auto 2rem auto;
    }
    
    .card-header {
        flex: 1;
        border-bottom: none;
        border-right: 1px solid #e9ecef;
    }
    
    .card-content {
        flex: 2;
        padding: 1.5rem;
    }
    
    .card-footer {
        flex-direction: row;
        justify-content: space-between;
        align-items: center;
        padding: 1.5rem;
    }
    
    .card-meta {
        flex-direction: row;
        gap: 1rem;
    }
    
    .card-actions {
        justify-content: flex-end;
    }
}

@media (min-width: 992px) {
    .card {
        max-width: 1000px;
    }
    
    .card-content {
        padding: 2rem;
    }
    
    .card-footer {
        padding: 2rem;
    }
}
```

### Feature Detection
```css
/* Feature detection with @supports */
@supports (display: grid) {
    .grid-layout {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        gap: 1rem;
    }
}

@supports not (display: grid) {
    .grid-layout {
        display: flex;
        flex-wrap: wrap;
        gap: 1rem;
    }
    
    .grid-layout > * {
        flex: 1 1 300px;
    }
}

/* CSS custom properties support */
@supports (--css: variables) {
    :root {
        --primary-color: #007bff;
        --secondary-color: #6c757d;
        --border-radius: 8px;
        --spacing: 1rem;
    }
    
    .enhanced-component {
        background: var(--primary-color);
        border-radius: var(--border-radius);
        padding: var(--spacing);
    }
}

@supports not (--css: variables) {
    .enhanced-component {
        background: #007bff;
        border-radius: 8px;
        padding: 1rem;
    }
}

/* Flexbox support */
@supports (display: flex) {
    .flex-layout {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
}

@supports not (display: flex) {
    .flex-layout {
        display: block;
    }
    
    .flex-layout > * {
        display: inline-block;
        vertical-align: middle;
    }
}
```

## Responsive Patterns

### Card Layout Patterns
```css
/* Mobile-first card grid */
.card-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 1rem;
    padding: 1rem;
}

.card-item {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
}

.card-image {
    width: 100%;
    height: 200px;
    object-fit: cover;
}

.card-content {
    padding: 1rem;
}

/* Progressive enhancement */
@media (min-width: 576px) {
    .card-grid {
        grid-template-columns: repeat(2, 1fr);
        gap: 1.5rem;
        padding: 1.5rem;
    }
}

@media (min-width: 768px) {
    .card-grid {
        grid-template-columns: repeat(3, 1fr);
        gap: 2rem;
        padding: 2rem;
    }
    
    .card-image {
        height: 250px;
    }
}

@media (min-width: 992px) {
    .card-grid {
        grid-template-columns: repeat(4, 1fr);
    }
    
    .card-image {
        height: 300px;
    }
}

@media (min-width: 1200px) {
    .card-grid {
        grid-template-columns: repeat(5, 1fr);
        max-width: 1400px;
        margin: 0 auto;
    }
}
```

### Navigation Patterns
```css
/* Mobile-first navigation */
.nav-mobile {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    z-index: 1000;
}

.nav-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    max-width: 1200px;
    margin: 0 auto;
}

.nav-brand {
    font-size: 1.25rem;
    font-weight: bold;
    color: #333;
}

.nav-toggle {
    display: block;
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    padding: 0.5rem;
}

.nav-menu {
    position: fixed;
    top: 100%;
    left: 0;
    right: 0;
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    transform: translateY(-100%);
    opacity: 0;
    visibility: hidden;
    transition: all 0.3s ease;
}

.nav-menu.active {
    transform: translateY(0);
    opacity: 1;
    visibility: visible;
}

.nav-menu-list {
    list-style: none;
    margin: 0;
    padding: 1rem;
}

.nav-menu-item {
    margin-bottom: 0.5rem;
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

/* Progressive enhancement for larger screens */
@media (min-width: 768px) {
    .nav-toggle {
        display: none;
    }
    
    .nav-menu {
        position: static;
        transform: none;
        opacity: 1;
        visibility: visible;
        box-shadow: none;
        background: transparent;
    }
    
    .nav-menu-list {
        display: flex;
        gap: 1rem;
        padding: 0;
    }
    
    .nav-menu-item {
        margin-bottom: 0;
    }
    
    .nav-menu-link {
        padding: 0.5rem 1rem;
    }
}
```

### Form Patterns
```css
/* Mobile-first form */
.form {
    max-width: 100%;
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
    font-size: 16px; /* Prevents zoom on iOS */
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

.form-textarea {
    min-height: 120px;
    resize: vertical;
}

.form-select {
    width: 100%;
    padding: 12px 16px;
    font-size: 16px;
    border: 2px solid #dee2e6;
    border-radius: 8px;
    background: white;
    cursor: pointer;
}

.form-checkbox,
.form-radio {
    margin-right: 0.5rem;
    transform: scale(1.2);
}

.form-checkbox-label,
.form-radio-label {
    display: flex;
    align-items: center;
    cursor: pointer;
    margin-bottom: 0.5rem;
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

.form-button-secondary {
    background: #6c757d;
    color: white;
}

.form-button-secondary:hover {
    background: #545b62;
}

/* Progressive enhancement for larger screens */
@media (min-width: 768px) {
    .form {
        max-width: 600px;
        margin: 0 auto;
        padding: 2rem;
    }
    
    .form-row {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 1rem;
    }
    
    .form-button {
        width: auto;
        min-width: 120px;
    }
    
    .form-actions {
        display: flex;
        gap: 1rem;
        justify-content: flex-end;
    }
}
```

## Exercise: Mobile-First Application

Create a mobile-first application with:
- Touch-friendly interface design
- Progressive enhancement for larger screens
- Feature detection with @supports
- Responsive navigation patterns
- Mobile-optimized forms
- Card layout patterns

## Key Takeaways
- Start with mobile styles and enhance for larger screens
- Design for touch interactions with appropriate target sizes
- Use progressive enhancement to add features for capable browsers
- Implement feature detection with @supports
- Create content-first, mobile-first HTML structure
- Test on real mobile devices, not just browser dev tools
- Consider performance implications of mobile-first approach
- Use appropriate breakpoints for your content and users
- Design for one-handed mobile usage
- Ensure accessibility across all device sizes
