# Media Queries Advanced Usage

## Introduction
Media queries are the foundation of responsive design, allowing you to apply different styles based on device characteristics. This lesson covers advanced media query techniques and responsive design patterns.

## Breakpoint Strategies

### Mobile-First Approach
```css
/* Base styles for mobile devices */
.container {
    width: 100%;
    padding: 1rem;
    margin: 0 auto;
}

.navigation {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
}

/* Small devices (landscape phones, 576px and up) */
@media (min-width: 576px) {
    .container {
        max-width: 540px;
        padding: 1.5rem;
    }
    
    .navigation {
        flex-direction: row;
        justify-content: space-between;
    }
}

/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) {
    .container {
        max-width: 720px;
        padding: 2rem;
    }
    
    .grid {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 2rem;
    }
}

/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) {
    .container {
        max-width: 960px;
    }
    
    .grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

/* Extra large devices (large desktops, 1200px and up) */
@media (min-width: 1200px) {
    .container {
        max-width: 1140px;
    }
    
    .grid {
        grid-template-columns: repeat(4, 1fr);
    }
}
```

**Code Explanation:**
- **Mobile-first approach**: Starts with mobile styles as base, then enhances for larger screens
- `width: 100%`: Full width for mobile devices
- `padding: 1rem`: Consistent padding using rem units
- `margin: 0 auto`: Centers container horizontally
- `@media (min-width: 576px)`: Applies styles when viewport is 576px or wider
- `max-width: 540px`: Constrains container width on small devices
- `flex-direction: column`: Stacks navigation items vertically on mobile
- `flex-direction: row`: Arranges navigation horizontally on larger screens
- `justify-content: space-between`: Distributes navigation items with space between
- `grid-template-columns: repeat(2, 1fr)`: Creates 2 equal-width columns
- `gap: 2rem`: Sets spacing between grid items
- `repeat(3, 1fr)` and `repeat(4, 1fr)`: Creates 3 and 4 column layouts respectively

**Benefits:**
- Mobile-first approach ensures optimal performance on mobile devices
- Progressive enhancement improves experience on larger screens
- Consistent breakpoints create predictable responsive behavior
- Flexible grid system adapts to different screen sizes
- Rem units provide scalable typography and spacing

### Desktop-First Approach
```css
/* Base styles for desktop */
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
}

.grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 2rem;
}

/* Large devices (large desktops, 1200px and down) */
@media (max-width: 1199.98px) {
    .container {
        max-width: 960px;
    }
    
    .grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

/* Medium devices (tablets, 768px and down) */
@media (max-width: 767.98px) {
    .container {
        max-width: 720px;
        padding: 1.5rem;
    }
    
    .grid {
        grid-template-columns: repeat(2, 1fr);
        gap: 1.5rem;
    }
}

/* Small devices (landscape phones, 576px and down) */
@media (max-width: 575.98px) {
    .container {
        max-width: 100%;
        padding: 1rem;
    }
    
    .grid {
        grid-template-columns: 1fr;
        gap: 1rem;
    }
}
```

## Feature Queries (@supports)

### Progressive Enhancement
```css
/* Base styles that work everywhere */
.card {
    background: white;
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 1rem;
}

/* Enhanced styles for browsers that support CSS Grid */
@supports (display: grid) {
    .card-container {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        gap: 1rem;
    }
}

/* Fallback for browsers without CSS Grid */
@supports not (display: grid) {
    .card-container {
        display: flex;
        flex-wrap: wrap;
        margin: -0.5rem;
    }
    
    .card-container .card {
        flex: 1 1 300px;
        margin: 0.5rem;
    }
}

/* Enhanced styles for browsers that support CSS custom properties */
@supports (--css: variables) {
    :root {
        --primary-color: #007bff;
        --secondary-color: #6c757d;
        --border-radius: 0.375rem;
    }
    
    .button {
        background-color: var(--primary-color);
        border-radius: var(--border-radius);
    }
}

/* Fallback for browsers without CSS custom properties */
@supports not (--css: variables) {
    .button {
        background-color: #007bff;
        border-radius: 0.375rem;
    }
}
```

### Advanced Feature Detection
```css
/* Check for multiple features */
@supports (display: grid) and (gap: 1rem) {
    .advanced-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
        gap: 1rem;
    }
}

/* Check for specific CSS functions */
@supports (width: clamp(100px, 50%, 500px)) {
    .responsive-width {
        width: clamp(100px, 50%, 500px);
    }
}

/* Check for backdrop-filter support */
@supports (backdrop-filter: blur(10px)) {
    .modal-overlay {
        backdrop-filter: blur(10px);
        background-color: rgba(0, 0, 0, 0.3);
    }
}

/* Fallback for browsers without backdrop-filter */
@supports not (backdrop-filter: blur(10px)) {
    .modal-overlay {
        background-color: rgba(0, 0, 0, 0.7);
    }
}
```

## Complex Media Query Conditions

### Combining Multiple Media Features
```css
/* High-resolution displays */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
    .high-res-image {
        background-image: url('image@2x.jpg');
    }
}

/* Landscape orientation on mobile */
@media (max-width: 768px) and (orientation: landscape) {
    .mobile-landscape {
        flex-direction: row;
        height: 100vh;
    }
}

/* Dark mode preference */
@media (prefers-color-scheme: dark) {
    :root {
        --bg-color: #1a1a1a;
        --text-color: #ffffff;
        --border-color: #333333;
    }
}

/* Reduced motion preference */
@media (prefers-reduced-motion: reduce) {
    * {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}

/* High contrast mode */
@media (prefers-contrast: high) {
    .button {
        border: 2px solid currentColor;
    }
    
    .card {
        border: 2px solid #000;
    }
}

/* Print styles */
@media print {
    .no-print {
        display: none !important;
    }
    
    .print-only {
        display: block !important;
    }
    
    body {
        font-size: 12pt;
        line-height: 1.4;
        color: #000;
        background: #fff;
    }
    
    .page-break {
        page-break-before: always;
    }
}
```

### Range Queries
```css
/* Range syntax for width */
@media (width >= 768px) and (width <= 1024px) {
    .tablet-layout {
        display: grid;
        grid-template-columns: 1fr 2fr;
    }
}

/* Range syntax for height */
@media (height >= 600px) {
    .tall-screen {
        padding-top: 2rem;
    }
}

/* Range syntax for aspect ratio */
@media (aspect-ratio >= 16/9) {
    .wide-screen {
        max-width: 1920px;
    }
}

/* Range syntax for resolution */
@media (resolution >= 2dppx) {
    .high-dpi {
        background-image: url('retina-image.jpg');
    }
}
```

## Responsive Typography

### Fluid Typography with clamp()
```css
/* Fluid typography that scales between breakpoints */
.heading-1 {
    font-size: clamp(1.5rem, 4vw, 3rem);
    line-height: 1.2;
}

.heading-2 {
    font-size: clamp(1.25rem, 3vw, 2.25rem);
    line-height: 1.3;
}

.heading-3 {
    font-size: clamp(1.125rem, 2.5vw, 1.875rem);
    line-height: 1.4;
}

.body-text {
    font-size: clamp(0.875rem, 2vw, 1.125rem);
    line-height: 1.6;
}

.small-text {
    font-size: clamp(0.75rem, 1.5vw, 0.875rem);
    line-height: 1.5;
}
```

### Responsive Typography Scale
```css
/* Modular scale for responsive typography */
:root {
    --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
    --text-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
    --text-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
    --text-lg: clamp(1.125rem, 1rem + 0.625vw, 1.25rem);
    --text-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem);
    --text-2xl: clamp(1.5rem, 1.3rem + 1vw, 1.875rem);
    --text-3xl: clamp(1.875rem, 1.6rem + 1.375vw, 2.25rem);
    --text-4xl: clamp(2.25rem, 1.9rem + 1.75vw, 3rem);
}

.heading-1 { font-size: var(--text-4xl); }
.heading-2 { font-size: var(--text-3xl); }
.heading-3 { font-size: var(--text-2xl); }
.heading-4 { font-size: var(--text-xl); }
.heading-5 { font-size: var(--text-lg); }
.heading-6 { font-size: var(--text-base); }

.body-large { font-size: var(--text-lg); }
.body { font-size: var(--text-base); }
.body-small { font-size: var(--text-sm); }
.caption { font-size: var(--text-xs); }
```

## Responsive Images and Media

### Responsive Images
```css
/* Responsive image container */
.image-container {
    position: relative;
    width: 100%;
    height: 0;
    padding-bottom: 56.25%; /* 16:9 aspect ratio */
    overflow: hidden;
}

.image-container img {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
}

/* Different aspect ratios for different screen sizes */
@media (max-width: 768px) {
    .image-container {
        padding-bottom: 75%; /* 4:3 aspect ratio on mobile */
    }
}

@media (min-width: 1200px) {
    .image-container {
        padding-bottom: 50%; /* 2:1 aspect ratio on large screens */
    }
}

/* Responsive background images */
.hero-section {
    background-image: url('hero-mobile.jpg');
    background-size: cover;
    background-position: center;
    min-height: 50vh;
}

@media (min-width: 768px) {
    .hero-section {
        background-image: url('hero-tablet.jpg');
        min-height: 60vh;
    }
}

@media (min-width: 1200px) {
    .hero-section {
        background-image: url('hero-desktop.jpg');
        min-height: 70vh;
    }
}
```

### Responsive Video
```css
/* Responsive video container */
.video-container {
    position: relative;
    width: 100%;
    height: 0;
    padding-bottom: 56.25%; /* 16:9 aspect ratio */
    overflow: hidden;
}

.video-container iframe,
.video-container video {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: none;
}

/* Different video sizes for different devices */
@media (max-width: 480px) {
    .video-container {
        padding-bottom: 75%; /* 4:3 aspect ratio on small phones */
    }
}
```

## Exercise: Responsive Website Layout

Create a responsive website layout with:
- Mobile-first approach with progressive enhancement
- Fluid typography using clamp() functions
- Responsive navigation that adapts to screen size
- Responsive image gallery with different layouts
- Print styles for better printing experience
- Dark mode support with media queries

## Key Takeaways
- Mobile-first approach provides better performance and user experience
- Use feature queries (@supports) for progressive enhancement
- Combine multiple media features for precise targeting
- Fluid typography with clamp() creates better responsive text
- Always provide fallbacks for unsupported features
- Test on real devices, not just browser dev tools
- Consider user preferences like reduced motion and high contrast
- Print styles improve the printing experience
