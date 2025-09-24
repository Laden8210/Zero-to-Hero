# Responsive Design Principles

## Introduction
Responsive design ensures your websites work beautifully across all devices and screen sizes. This lesson covers media queries, flexible layouts, and mobile-first development approaches.

## Media Queries Advanced Usage

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

### Feature Queries (@supports)
```css
/* Progressive enhancement with feature queries */
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
```

## Responsive Units and Values

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

### Responsive Spacing
```css
/* Responsive spacing using viewport units */
.section {
    padding: clamp(1rem, 5vw, 4rem);
    margin: clamp(0.5rem, 3vw, 2rem) 0;
}

.container {
    max-width: min(1200px, 90vw);
    margin: 0 auto;
    padding: 0 clamp(1rem, 4vw, 2rem);
}

/* Responsive gaps */
.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: clamp(1rem, 3vw, 2rem);
}

/* Responsive margins */
.card {
    margin: clamp(0.5rem, 2vw, 1rem);
}
```

### Container Queries
```css
/* Container queries for component-level responsiveness */
.card-container {
    container-type: inline-size;
}

.card {
    display: flex;
    flex-direction: column;
    padding: 1rem;
}

/* When container is wider than 300px */
@container (min-width: 300px) {
    .card {
        flex-direction: row;
        align-items: center;
    }
    
    .card__image {
        width: 100px;
        height: 100px;
        margin-right: 1rem;
    }
}

/* When container is wider than 500px */
@container (min-width: 500px) {
    .card {
        padding: 1.5rem;
    }
    
    .card__image {
        width: 150px;
        height: 150px;
    }
}
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

## Responsive Layout Patterns

### Responsive Navigation
```css
/* Mobile-first navigation */
.nav {
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.nav__container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
}

.nav__brand {
    font-size: 1.25rem;
    font-weight: bold;
    color: #333;
}

.nav__menu {
    display: none;
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background: white;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
}

.nav__menu--open {
    display: block;
}

.nav__toggle {
    display: block;
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
}

/* Tablet and desktop navigation */
@media (min-width: 768px) {
    .nav__menu {
        display: flex;
        position: static;
        box-shadow: none;
        padding: 0;
        gap: 2rem;
    }
    
    .nav__toggle {
        display: none;
    }
}
```

### Responsive Grid Systems
```css
/* Mobile-first grid system */
.grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 1rem;
    padding: 1rem;
}

/* Small screens */
@media (min-width: 576px) {
    .grid {
        grid-template-columns: repeat(2, 1fr);
        gap: 1.5rem;
    }
}

/* Medium screens */
@media (min-width: 768px) {
    .grid {
        grid-template-columns: repeat(3, 1fr);
        gap: 2rem;
    }
}

/* Large screens */
@media (min-width: 992px) {
    .grid {
        grid-template-columns: repeat(4, 1fr);
    }
}

/* Extra large screens */
@media (min-width: 1200px) {
    .grid {
        grid-template-columns: repeat(5, 1fr);
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
- Fluid typography with clamp() creates better responsive text
- Container queries enable component-level responsiveness
- Always provide fallbacks for unsupported features
- Test on real devices, not just browser dev tools
- Consider user preferences like reduced motion and high contrast
- Use responsive images and media for better performance
- Implement responsive navigation patterns
- Create flexible grid systems that adapt to content
