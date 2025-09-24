# BEM (Block Element Modifier) Methodology

## Introduction
BEM is a CSS methodology that provides a structured approach to naming CSS classes. It stands for Block, Element, Modifier and helps create maintainable, scalable CSS code.

## BEM Naming Convention

### Basic BEM Structure
```css
/* Block: standalone component */
.button {
    display: inline-block;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
    text-decoration: none;
    transition: all 0.3s ease;
}

/* Element: part of a block */
.button__icon {
    margin-right: 0.5rem;
    vertical-align: middle;
}

.button__text {
    font-weight: 500;
}

/* Modifier: variant of a block or element */
.button--primary {
    background-color: #007bff;
    color: white;
}

.button--primary:hover {
    background-color: #0056b3;
}

.button--secondary {
    background-color: #6c757d;
    color: white;
}

.button--large {
    padding: 0.75rem 1.5rem;
    font-size: 1.125rem;
}

.button--small {
    padding: 0.25rem 0.75rem;
    font-size: 0.875rem;
}

.button--disabled {
    opacity: 0.6;
    cursor: not-allowed;
    pointer-events: none;
}
```

**Code Explanation:**
- `.button`: **Block** - standalone component that can be reused independently
- `.button__icon`: **Element** - part of the button block (double underscore separator)
- `.button__text`: **Element** - text content within the button block
- `.button--primary`: **Modifier** - variant of the button block (double hyphen separator)
- `.button--secondary`: **Modifier** - different style variant of the button
- `.button--large`: **Modifier** - size variant of the button
- `.button--small`: **Modifier** - smaller size variant
- `.button--disabled`: **Modifier** - disabled state of the button
- `display: inline-block`: Allows button to have width/height while staying inline
- `padding: 0.5rem 1rem`: Vertical and horizontal padding
- `border-radius: 4px`: Rounded corners
- `transition: all 0.3s ease`: Smooth transitions for all properties
- `pointer-events: none`: Prevents interaction with disabled elements

**Benefits:**
- Clear naming convention makes CSS self-documenting
- Prevents CSS conflicts through unique class names
- Easy to understand component structure
- Modular approach enables component reuse
- Maintainable and scalable CSS architecture

### HTML Structure with BEM
```html
<!-- Basic button -->
<button class="button">
    <span class="button__text">Click me</span>
</button>

<!-- Button with icon -->
<button class="button button--primary">
    <i class="button__icon">📧</i>
    <span class="button__text">Send Email</span>
</button>

<!-- Large secondary button -->
<button class="button button--secondary button--large">
    <span class="button__text">Large Button</span>
</button>

<!-- Disabled button -->
<button class="button button--primary button--disabled">
    <span class="button__text">Disabled</span>
</button>
```

## Complex BEM Examples

### Card Component
```css
/* Block: Card */
.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    transition: box-shadow 0.3s ease;
}

.card:hover {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

/* Elements */
.card__header {
    padding: 1rem 1.5rem;
    border-bottom: 1px solid #e9ecef;
    background-color: #f8f9fa;
}

.card__title {
    margin: 0;
    font-size: 1.25rem;
    font-weight: 600;
    color: #212529;
}

.card__subtitle {
    margin: 0.25rem 0 0 0;
    font-size: 0.875rem;
    color: #6c757d;
}

.card__body {
    padding: 1.5rem;
}

.card__content {
    margin-bottom: 1rem;
    line-height: 1.6;
    color: #495057;
}

.card__footer {
    padding: 1rem 1.5rem;
    border-top: 1px solid #e9ecef;
    background-color: #f8f9fa;
}

.card__actions {
    display: flex;
    gap: 0.5rem;
    justify-content: flex-end;
}

/* Modifiers */
.card--elevated {
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.card--outlined {
    border: 1px solid #dee2e6;
    box-shadow: none;
}

.card--compact .card__header,
.card--compact .card__body,
.card--compact .card__footer {
    padding: 0.75rem 1rem;
}

.card--horizontal {
    display: flex;
    flex-direction: row;
}

.card--horizontal .card__body {
    flex: 1;
}

.card--horizontal .card__image {
    width: 200px;
    height: auto;
    object-fit: cover;
}
```

### HTML for Card Component
```html
<!-- Basic card -->
<div class="card">
    <div class="card__header">
        <h3 class="card__title">Card Title</h3>
        <p class="card__subtitle">Card subtitle</p>
    </div>
    <div class="card__body">
        <p class="card__content">This is the main content of the card.</p>
    </div>
    <div class="card__footer">
        <div class="card__actions">
            <button class="button button--primary">Action</button>
            <button class="button button--secondary">Cancel</button>
        </div>
    </div>
</div>

<!-- Elevated card -->
<div class="card card--elevated">
    <div class="card__header">
        <h3 class="card__title">Elevated Card</h3>
    </div>
    <div class="card__body">
        <p class="card__content">This card has more elevation.</p>
    </div>
</div>

<!-- Horizontal card -->
<div class="card card--horizontal">
    <img src="image.jpg" alt="Card image" class="card__image">
    <div class="card__body">
        <h3 class="card__title">Horizontal Card</h3>
        <p class="card__content">This card displays horizontally.</p>
    </div>
</div>
```

### Navigation Component
```css
/* Block: Navigation */
.nav {
    background-color: #fff;
    border-bottom: 1px solid #e9ecef;
    padding: 0 1rem;
}

/* Elements */
.nav__container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    max-width: 1200px;
    margin: 0 auto;
    height: 60px;
}

.nav__brand {
    font-size: 1.5rem;
    font-weight: bold;
    color: #007bff;
    text-decoration: none;
}

.nav__menu {
    display: flex;
    list-style: none;
    margin: 0;
    padding: 0;
    gap: 2rem;
}

.nav__item {
    position: relative;
}

.nav__link {
    display: block;
    padding: 0.5rem 0;
    color: #495057;
    text-decoration: none;
    font-weight: 500;
    transition: color 0.3s ease;
}

.nav__link:hover {
    color: #007bff;
}

.nav__dropdown {
    position: absolute;
    top: 100%;
    left: 0;
    background: white;
    border: 1px solid #e9ecef;
    border-radius: 4px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    min-width: 200px;
    opacity: 0;
    visibility: hidden;
    transform: translateY(-10px);
    transition: all 0.3s ease;
}

.nav__dropdown-item {
    display: block;
    padding: 0.75rem 1rem;
    color: #495057;
    text-decoration: none;
    border-bottom: 1px solid #f8f9fa;
}

.nav__dropdown-item:hover {
    background-color: #f8f9fa;
    color: #007bff;
}

.nav__dropdown-item:last-child {
    border-bottom: none;
}

/* Modifiers */
.nav--dark {
    background-color: #343a40;
    border-bottom-color: #495057;
}

.nav--dark .nav__brand {
    color: #fff;
}

.nav--dark .nav__link {
    color: #adb5bd;
}

.nav--dark .nav__link:hover {
    color: #fff;
}

.nav--dark .nav__dropdown {
    background-color: #343a40;
    border-color: #495057;
}

.nav--dark .nav__dropdown-item {
    color: #adb5bd;
    border-bottom-color: #495057;
}

.nav--dark .nav__dropdown-item:hover {
    background-color: #495057;
    color: #fff;
}

.nav--fixed {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1000;
}

.nav--transparent {
    background-color: transparent;
    border-bottom: none;
}

/* Responsive modifiers */
@media (max-width: 768px) {
    .nav__menu {
        position: fixed;
        top: 60px;
        left: 0;
        right: 0;
        background: white;
        flex-direction: column;
        padding: 1rem;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        transform: translateX(-100%);
        transition: transform 0.3s ease;
    }
    
    .nav__menu--open {
        transform: translateX(0);
    }
    
    .nav__item {
        margin-bottom: 0.5rem;
    }
    
    .nav__link {
        padding: 0.75rem 0;
        border-bottom: 1px solid #e9ecef;
    }
}
```

## BEM Best Practices

### Naming Conventions
```css
/* Good BEM naming */
.block {}
.block__element {}
.block--modifier {}
.block__element--modifier {}

/* Bad BEM naming - avoid these */
.block__element__subelement {} /* Too deep nesting */
.block--modifier--another {} /* Multiple modifiers on block */
.block__element_modifier {} /* Wrong separator */
```

### Avoiding Deep Nesting
```css
/* Good: Flat structure */
.card {}
.card__header {}
.card__title {}
.card__subtitle {}
.card__body {}
.card__content {}
.card__footer {}
.card__actions {}

/* Bad: Deep nesting */
.card {}
.card__header {}
.card__header__title {}
.card__header__subtitle {}
.card__body {}
.card__body__content {}
.card__footer {}
.card__footer__actions {}
```

### Modifier Combinations
```css
/* Multiple modifiers on the same element */
.button {}
.button--primary {}
.button--large {}
.button--disabled {}

/* Combined modifiers */
.button--primary.button--large {
    /* Styles for primary AND large button */
}

.button--primary.button--disabled {
    /* Styles for primary AND disabled button */
}

/* Element with modifier */
.button__icon {}
.button__icon--left {}
.button__icon--right {}
```

## BEM with CSS Preprocessors

### Sass/SCSS Implementation
```scss
// BEM with Sass nesting
.button {
    display: inline-block;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
    text-decoration: none;
    transition: all 0.3s ease;
    
    // Elements
    &__icon {
        margin-right: 0.5rem;
        vertical-align: middle;
    }
    
    &__text {
        font-weight: 500;
    }
    
    // Modifiers
    &--primary {
        background-color: #007bff;
        color: white;
        
        &:hover {
            background-color: #0056b3;
        }
    }
    
    &--secondary {
        background-color: #6c757d;
        color: white;
        
        &:hover {
            background-color: #545b62;
        }
    }
    
    &--large {
        padding: 0.75rem 1.5rem;
        font-size: 1.125rem;
    }
    
    &--small {
        padding: 0.25rem 0.75rem;
        font-size: 0.875rem;
    }
    
    &--disabled {
        opacity: 0.6;
        cursor: not-allowed;
        pointer-events: none;
    }
}
```

## Exercise: BEM Component Library

Create a component library using BEM methodology:
- Button component with multiple variants
- Card component with different styles
- Navigation component with dropdown
- Form components with validation states
- Modal component with overlay
- Grid system with BEM naming

## Key Takeaways
- BEM provides clear, maintainable CSS naming conventions
- Block: standalone component, Element: part of block, Modifier: variant
- Use double underscore (__) for elements, double hyphen (--) for modifiers
- Avoid deep nesting and keep structure flat
- BEM works well with CSS preprocessors
- Consistent naming makes CSS more maintainable
- BEM helps prevent CSS conflicts and specificity issues
- Use modifiers to create variations of components
