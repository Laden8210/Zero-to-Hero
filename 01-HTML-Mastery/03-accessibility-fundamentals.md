# Accessibility Fundamentals

## Introduction
Accessibility is crucial for creating inclusive web experiences. This lesson covers ARIA landmarks, keyboard navigation, screen reader support, and WCAG guidelines to ensure your websites are accessible to all users.

## ARIA Landmarks and Roles

### Document Structure with ARIA
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Accessible Web Page</title>
</head>
<body>
    <!-- Skip link for keyboard navigation -->
    <a href="#main-content" class="skip-link">Skip to main content</a>
    
    <!-- Header landmark -->
    <header role="banner">
        <h1>Website Title</h1>
        <nav role="navigation" aria-label="Main navigation">
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#about">About</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- Main content landmark -->
    <main id="main-content" role="main">
        <article>
            <header>
                <h2>Article Title</h2>
                <time datetime="2024-01-15">January 15, 2024</time>
            </header>
            
            <section>
                <h3>Section Title</h3>
                <p>Article content goes here...</p>
            </section>
        </article>

        <!-- Complementary content -->
        <aside role="complementary" aria-label="Related articles">
            <h3>Related Articles</h3>
            <ul>
                <li><a href="#">Related Article 1</a></li>
                <li><a href="#">Related Article 2</a></li>
            </ul>
        </aside>
    </main>

    <!-- Footer landmark -->
    <footer role="contentinfo">
        <p>&copy; 2024 Your Website. All rights reserved.</p>
        <address>
            Contact: <a href="mailto:contact@example.com">contact@example.com</a>
        </address>
    </footer>
</body>
</html>
```

### ARIA Labels and Descriptions
```html
<!-- Form with ARIA labels -->
<form>
    <div class="form-group">
        <label for="username">Username</label>
        <input 
            type="text" 
            id="username" 
            name="username"
            aria-describedby="username-help username-error"
            aria-required="true"
            aria-invalid="false"
        >
        <div id="username-help" class="help-text">
            Choose a unique username (3-20 characters)
        </div>
        <div id="username-error" class="error-text" role="alert" aria-live="polite"></div>
    </div>

    <!-- Password with strength indicator -->
    <div class="form-group">
        <label for="password">Password</label>
        <input 
            type="password" 
            id="password" 
            name="password"
            aria-describedby="password-help password-strength"
            aria-required="true"
        >
        <div id="password-help" class="help-text">
            Password must be at least 8 characters
        </div>
        <div id="password-strength" aria-live="polite">
            Password strength: <span id="strength-text">Not set</span>
        </div>
    </div>

    <!-- Custom checkbox with ARIA -->
    <div class="form-group">
        <div class="checkbox-wrapper">
            <input 
                type="checkbox" 
                id="newsletter" 
                name="newsletter"
                aria-describedby="newsletter-desc"
            >
            <label for="newsletter">Subscribe to Newsletter</label>
        </div>
        <div id="newsletter-desc" class="help-text">
            Receive weekly updates about new features and content
        </div>
    </div>
</form>
```

### ARIA States and Properties
```html
<!-- Dynamic content with ARIA states -->
<div class="accordion">
    <button 
        class="accordion-trigger"
        aria-expanded="false"
        aria-controls="panel-1"
        id="trigger-1"
    >
        <span>Section 1</span>
        <span class="accordion-icon" aria-hidden="true">▼</span>
    </button>
    
    <div 
        id="panel-1"
        class="accordion-panel"
        aria-labelledby="trigger-1"
        aria-hidden="true"
        role="region"
    >
        <p>Content for section 1...</p>
    </div>
</div>

<!-- Progress indicator -->
<div class="progress-container">
    <label for="progress-bar">Upload Progress</label>
    <div 
        id="progress-bar"
        class="progress-bar"
        role="progressbar"
        aria-valuenow="45"
        aria-valuemin="0"
        aria-valuemax="100"
        aria-label="Upload progress: 45%"
    >
        <div class="progress-fill" style="width: 45%"></div>
    </div>
    <div class="progress-text">45% complete</div>
</div>

<!-- Live region for dynamic updates -->
<div 
    id="status-messages"
    class="status-messages"
    aria-live="polite"
    aria-atomic="true"
    role="status"
>
    <!-- Status messages will be inserted here -->
</div>
```

## Keyboard Navigation

### Tabindex Management
```html
<!-- Custom interactive elements -->
<div class="custom-widget">
    <button 
        class="widget-trigger"
        tabindex="0"
        aria-expanded="false"
        aria-haspopup="true"
        aria-controls="widget-menu"
    >
        Open Menu
    </button>
    
    <div 
        id="widget-menu"
        class="widget-menu"
        role="menu"
        aria-hidden="true"
    >
        <button role="menuitem" tabindex="-1">Option 1</button>
        <button role="menuitem" tabindex="-1">Option 2</button>
        <button role="menuitem" tabindex="-1">Option 3</button>
    </div>
</div>

<!-- Modal with proper focus management -->
<div 
    class="modal-overlay"
    role="dialog"
    aria-modal="true"
    aria-labelledby="modal-title"
    aria-describedby="modal-description"
    tabindex="-1"
>
    <div class="modal-content">
        <h2 id="modal-title">Confirm Action</h2>
        <p id="modal-description">Are you sure you want to delete this item?</p>
        
        <div class="modal-actions">
            <button class="btn-primary" tabindex="0">Confirm</button>
            <button class="btn-secondary" tabindex="0">Cancel</button>
        </div>
        
        <button 
            class="modal-close"
            aria-label="Close modal"
            tabindex="0"
        >
            ×
        </button>
    </div>
</div>
```

### Focus Management JavaScript
```javascript
// Focus management utilities
class FocusManager {
    constructor() {
        this.focusableElements = 'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])';
        this.previouslyFocusedElement = null;
    }

    // Trap focus within an element
    trapFocus(element) {
        const focusableElements = element.querySelectorAll(this.focusableElements);
        const firstFocusableElement = focusableElements[0];
        const lastFocusableElement = focusableElements[focusableElements.length - 1];

        element.addEventListener('keydown', (e) => {
            if (e.key === 'Tab') {
                if (e.shiftKey) {
                    // Shift + Tab
                    if (document.activeElement === firstFocusableElement) {
                        lastFocusableElement.focus();
                        e.preventDefault();
                    }
                } else {
                    // Tab
                    if (document.activeElement === lastFocusableElement) {
                        firstFocusableElement.focus();
                        e.preventDefault();
                    }
                }
            }
        });

        // Focus first element
        firstFocusableElement.focus();
    }

    // Save and restore focus
    saveFocus() {
        this.previouslyFocusedElement = document.activeElement;
    }

    restoreFocus() {
        if (this.previouslyFocusedElement) {
            this.previouslyFocusedElement.focus();
        }
    }

    // Move focus to element
    moveFocus(element) {
        if (element) {
            element.focus();
        }
    }
}

// Usage example
const focusManager = new FocusManager();

// Modal focus management
function openModal(modalElement) {
    focusManager.saveFocus();
    modalElement.style.display = 'block';
    focusManager.trapFocus(modalElement);
}

function closeModal(modalElement) {
    modalElement.style.display = 'none';
    focusManager.restoreFocus();
}
```

## Screen Reader Support

### Semantic HTML for Screen Readers
```html
<!-- Data table with proper headers -->
<table>
    <caption>Monthly Sales Report</caption>
    <thead>
        <tr>
            <th scope="col">Month</th>
            <th scope="col">Sales</th>
            <th scope="col">Growth</th>
            <th scope="col">Actions</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">January</th>
            <td>$10,000</td>
            <td>+5%</td>
            <td>
                <button aria-label="Edit January sales data">Edit</button>
                <button aria-label="Delete January sales data">Delete</button>
            </td>
        </tr>
        <tr>
            <th scope="row">February</th>
            <td>$12,000</td>
            <td>+20%</td>
            <td>
                <button aria-label="Edit February sales data">Edit</button>
                <button aria-label="Delete February sales data">Delete</button>
            </td>
        </tr>
    </tbody>
</table>

<!-- List with proper structure -->
<nav aria-label="Breadcrumb navigation">
    <ol class="breadcrumb">
        <li><a href="/">Home</a></li>
        <li><a href="/products">Products</a></li>
        <li><a href="/products/electronics">Electronics</a></li>
        <li aria-current="page">Smartphones</li>
    </ol>
</nav>

<!-- Definition list -->
<dl>
    <dt>HTML</dt>
    <dd>HyperText Markup Language - the standard markup language for web pages</dd>
    
    <dt>CSS</dt>
    <dd>Cascading Style Sheets - used for styling web pages</dd>
    
    <dt>JavaScript</dt>
    <dd>A programming language that enables interactive web pages</dd>
</dl>
```

### Hidden Content for Screen Readers
```html
<!-- Visually hidden but accessible to screen readers -->
<span class="sr-only">This text is only visible to screen readers</span>

<!-- Skip links -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<!-- Screen reader only text -->
<button>
    <span class="sr-only">Add item to cart</span>
    <span aria-hidden="true">🛒</span>
</button>

<!-- Complex data visualization -->
<div class="chart-container">
    <h3>Sales Chart</h3>
    <div class="chart" aria-hidden="true">
        <!-- Visual chart elements -->
    </div>
    <div class="chart-data sr-only">
        <p>January sales: $10,000, February sales: $12,000, March sales: $15,000</p>
    </div>
</div>
```

## WCAG Guidelines Implementation

### Color Contrast and Visual Design
```css
/* High contrast color scheme */
:root {
    --text-primary: #000000;
    --text-secondary: #333333;
    --background-primary: #ffffff;
    --background-secondary: #f5f5f5;
    --border-color: #cccccc;
    --focus-color: #0066cc;
}

/* Focus indicators */
*:focus {
    outline: 2px solid var(--focus-color);
    outline-offset: 2px;
}

/* High contrast mode support */
@media (prefers-contrast: high) {
    :root {
        --text-primary: #000000;
        --text-secondary: #000000;
        --background-primary: #ffffff;
        --background-secondary: #ffffff;
        --border-color: #000000;
    }
}

/* Reduced motion support */
@media (prefers-reduced-motion: reduce) {
    * {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}

/* Screen reader only content */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}

/* Skip links */
.skip-link {
    position: absolute;
    top: -40px;
    left: 6px;
    background: var(--focus-color);
    color: white;
    padding: 8px;
    text-decoration: none;
    z-index: 1000;
}

.skip-link:focus {
    top: 6px;
}
```

### Error Handling and Validation
```html
<!-- Form with comprehensive error handling -->
<form novalidate>
    <div class="form-group">
        <label for="email">Email Address *</label>
        <input 
            type="email" 
            id="email" 
            name="email"
            required
            aria-describedby="email-error email-help"
            aria-invalid="false"
        >
        <div id="email-help" class="help-text">
            We'll never share your email with anyone else
        </div>
        <div id="email-error" class="error-text" role="alert" aria-live="polite"></div>
    </div>

    <div class="form-group">
        <label for="password">Password *</label>
        <input 
            type="password" 
            id="password" 
            name="password"
            required
            minlength="8"
            aria-describedby="password-error password-requirements"
            aria-invalid="false"
        >
        <div id="password-requirements" class="help-text">
            Password must be at least 8 characters long
        </div>
        <div id="password-error" class="error-text" role="alert" aria-live="polite"></div>
    </div>

    <button type="submit">Create Account</button>
</form>
```

## Exercise: Accessible Web Application

Create an accessible web application with:
- Proper ARIA landmarks and roles
- Keyboard navigation support
- Screen reader compatibility
- High contrast mode support
- Focus management
- Error handling with ARIA
- Skip links and navigation aids

## Key Takeaways
- Use semantic HTML elements for better accessibility
- Implement ARIA landmarks and roles appropriately
- Ensure keyboard navigation works for all interactive elements
- Provide clear focus indicators and focus management
- Support screen readers with proper labeling and descriptions
- Follow WCAG guidelines for color contrast and visual design
- Test with actual assistive technologies
- Provide alternative text for images and media
- Use live regions for dynamic content updates
- Implement proper error handling and validation feedback
