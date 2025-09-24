# Responsive Units and Values

## Introduction
Understanding responsive units and values is crucial for creating flexible, scalable designs. This lesson covers relative units, calculation functions, and modern CSS features for responsive design.

## Relative Units Deep Dive

### Viewport Units
```css
/* Viewport width and height units */
.full-viewport {
    width: 100vw;  /* 100% of viewport width */
    height: 100vh; /* 100% of viewport height */
}

.half-viewport {
    width: 50vw;   /* 50% of viewport width */
    height: 50vh;  /* 50% of viewport height */
}

/* Viewport minimum and maximum */
.min-viewport {
    width: 100vmin;  /* 100% of smaller viewport dimension */
    height: 100vmax; /* 100% of larger viewport dimension */
}

/* Responsive typography with viewport units */
.responsive-heading {
    font-size: 4vw; /* Scales with viewport width */
    line-height: 1.2;
}

.responsive-text {
    font-size: 2vw;
    line-height: 1.5;
}

/* Responsive spacing */
.viewport-spacing {
    padding: 5vh 5vw; /* Responsive padding */
    margin: 2vh 0;    /* Responsive margin */
}
```

### Relative Units (em, rem, %)
```css
/* Base font size */
html {
    font-size: 16px; /* 1rem = 16px */
}

/* Em units - relative to parent font size */
.em-container {
    font-size: 20px; /* Parent font size */
}

.em-child {
    font-size: 1.5em;  /* 30px (1.5 × 20px) */
    padding: 0.5em;    /* 10px (0.5 × 20px) */
    margin: 1em;       /* 20px (1 × 20px) */
}

/* Rem units - relative to root font size */
.rem-child {
    font-size: 1.5rem;  /* 24px (1.5 × 16px) */
    padding: 0.5rem;    /* 8px (0.5 × 16px) */
    margin: 1rem;       /* 16px (1 × 16px) */
}

/* Percentage units */
.percentage-container {
    width: 100%;
    height: 400px;
}

.percentage-child {
    width: 50%;        /* 50% of parent width */
    height: 25%;       /* 25% of parent height */
    padding: 2%;       /* 2% of parent width */
}
```

### Modern CSS Units
```css
/* Container query units (modern browsers) */
.container {
    container-type: inline-size;
    width: 100%;
}

.container-child {
    /* These units are relative to the container, not viewport */
    width: 50cqw;  /* 50% of container width */
    height: 30cqh; /* 30% of container height */
    font-size: 2cqi; /* 2% of container inline size */
}

/* Logical units for internationalization */
.logical-units {
    /* Writing mode aware */
    margin-inline-start: 1rem;  /* Left in LTR, right in RTL */
    margin-inline-end: 1rem;    /* Right in LTR, left in RTL */
    margin-block-start: 2rem;   /* Top in horizontal writing */
    margin-block-end: 2rem;     /* Bottom in horizontal writing */
    
    /* Logical dimensions */
    inline-size: 300px;  /* Width in horizontal writing */
    block-size: 200px;   /* Height in horizontal writing */
}

/* Grid and flexbox units */
.grid-container {
    display: grid;
    grid-template-columns: 1fr 2fr 1fr; /* Fractional units */
    gap: 1rem;
}

.flex-container {
    display: flex;
    flex: 1 1 auto; /* flex-grow flex-shrink flex-basis */
}
```

## Calculation Functions

### calc() Function
```css
/* Basic calculations */
.calc-basic {
    width: calc(100% - 2rem);     /* Full width minus 2rem */
    height: calc(100vh - 60px);   /* Full height minus header */
    margin: calc(1rem + 2px);     /* Addition */
    padding: calc(2rem - 1rem);   /* Subtraction */
}

/* Complex calculations */
.calc-complex {
    width: calc(50% + 100px);     /* Half width plus fixed amount */
    height: calc(100vh - 2rem - 60px); /* Multiple subtractions */
    font-size: calc(1rem + 0.5vw);     /* Responsive font size */
    margin: calc(1rem * 2);             /* Multiplication */
}

/* Responsive layouts with calc() */
.responsive-layout {
    display: grid;
    grid-template-columns: 
        calc(100% - 300px) 300px; /* Content and sidebar */
    gap: 1rem;
}

@media (max-width: 768px) {
    .responsive-layout {
        grid-template-columns: 1fr; /* Single column on mobile */
    }
}

/* Dynamic spacing */
.dynamic-spacing {
    padding: calc(1rem + 2vw);    /* Responsive padding */
    margin: calc(0.5rem + 1vh);   /* Responsive margin */
    gap: calc(1rem + 0.5vw);      /* Responsive gap */
}
```

### min(), max(), and clamp() Functions
```css
/* min() function - takes the smallest value */
.min-example {
    width: min(100%, 500px);      /* Never wider than 500px */
    height: min(50vh, 300px);     /* Never taller than 300px */
    font-size: min(2rem, 4vw);    /* Responsive but capped */
}

/* max() function - takes the largest value */
.max-example {
    width: max(300px, 50%);       /* At least 300px wide */
    height: max(200px, 30vh);     /* At least 200px tall */
    font-size: max(1rem, 2vw);    /* At least 1rem */
}

/* clamp() function - constrained between min and max */
.clamp-example {
    width: clamp(300px, 50%, 800px);    /* Between 300px and 800px */
    height: clamp(200px, 30vh, 500px);  /* Between 200px and 500px */
    font-size: clamp(1rem, 2.5vw, 2rem); /* Between 1rem and 2rem */
}

/* Fluid typography with clamp() */
.fluid-heading {
    font-size: clamp(1.5rem, 4vw, 3rem);
    line-height: clamp(1.2, 1.1 + 0.5vw, 1.4);
}

.fluid-text {
    font-size: clamp(0.875rem, 2vw, 1.125rem);
    line-height: clamp(1.4, 1.2 + 0.5vw, 1.6);
}

/* Responsive spacing with clamp() */
.fluid-spacing {
    padding: clamp(1rem, 5vw, 4rem);
    margin: clamp(0.5rem, 3vw, 2rem);
    gap: clamp(1rem, 3vw, 2rem);
}
```

### Advanced Calculation Patterns
```css
/* Aspect ratio calculations */
.aspect-ratio-16-9 {
    width: 100%;
    height: calc(100% * 9 / 16); /* 16:9 aspect ratio */
}

.aspect-ratio-4-3 {
    width: 100%;
    height: calc(100% * 3 / 4); /* 4:3 aspect ratio */
}

/* Golden ratio calculations */
.golden-ratio {
    width: 100%;
    height: calc(100% * 0.618); /* Golden ratio (φ) */
}

/* Responsive grid with calculations */
.calculated-grid {
    display: grid;
    grid-template-columns: repeat(
        auto-fit, 
        minmax(calc(300px - 2rem), 1fr)
    );
    gap: 1rem;
    padding: 1rem;
}

/* Dynamic border radius */
.dynamic-radius {
    border-radius: calc(0.5rem + 1vw);
}

/* Responsive shadows */
.dynamic-shadow {
    box-shadow: 
        0 calc(0.5rem + 0.5vw) calc(1rem + 1vw) rgba(0, 0, 0, 0.1);
}
```

## Container Queries

### Basic Container Queries
```css
/* Container setup */
.card-container {
    container-type: inline-size;
    width: 100%;
}

.card {
    padding: 1rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* Container queries */
@container (min-width: 300px) {
    .card {
        padding: 1.5rem;
        display: flex;
        align-items: center;
    }
    
    .card-image {
        width: 80px;
        height: 80px;
        margin-right: 1rem;
    }
}

@container (min-width: 500px) {
    .card {
        padding: 2rem;
        flex-direction: column;
        text-align: center;
    }
    
    .card-image {
        width: 120px;
        height: 120px;
        margin-right: 0;
        margin-bottom: 1rem;
    }
}

@container (min-width: 700px) {
    .card {
        padding: 2.5rem;
        flex-direction: row;
        text-align: left;
    }
    
    .card-image {
        width: 150px;
        height: 150px;
        margin-right: 2rem;
        margin-bottom: 0;
    }
}
```

### Advanced Container Queries
```css
/* Multiple container types */
.complex-container {
    container-type: inline-size block-size;
    width: 100%;
    height: 100%;
}

.complex-child {
    padding: 1rem;
}

/* Combined container queries */
@container (min-width: 300px) and (min-height: 200px) {
    .complex-child {
        padding: 1.5rem;
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 1rem;
    }
}

@container (min-width: 500px) and (min-height: 300px) {
    .complex-child {
        padding: 2rem;
        grid-template-columns: 1fr 2fr;
        gap: 2rem;
    }
}

/* Container query units */
.container-units {
    container-type: inline-size;
}

.container-units-child {
    width: 50cqw;        /* 50% of container width */
    height: 30cqh;       /* 30% of container height */
    font-size: 2cqi;     /* 2% of container inline size */
    padding: 1cqw;       /* 1% of container width */
    margin: 0.5cqh;      /* 0.5% of container height */
}
```

## Responsive Design Patterns

### Mobile-First Typography Scale
```css
/* Mobile-first typography */
:root {
    --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
    --text-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
    --text-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
    --text-lg: clamp(1.125rem, 1rem + 0.625vw, 1.25rem);
    --text-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem);
    --text-2xl: clamp(1.5rem, 1.3rem + 1vw, 1.875rem);
    --text-3xl: clamp(1.875rem, 1.6rem + 1.375vw, 2.25rem);
    --text-4xl: clamp(2.25rem, 1.9rem + 1.75vw, 3rem);
    --text-5xl: clamp(3rem, 2.5rem + 2.5vw, 4rem);
}

.heading-1 { font-size: var(--text-5xl); }
.heading-2 { font-size: var(--text-4xl); }
.heading-3 { font-size: var(--text-3xl); }
.heading-4 { font-size: var(--text-2xl); }
.heading-5 { font-size: var(--text-xl); }
.heading-6 { font-size: var(--text-lg); }

.body-large { font-size: var(--text-lg); }
.body { font-size: var(--text-base); }
.body-small { font-size: var(--text-sm); }
.caption { font-size: var(--text-xs); }
```

### Responsive Spacing System
```css
/* Responsive spacing scale */
:root {
    --space-xs: clamp(0.25rem, 0.2rem + 0.25vw, 0.5rem);
    --space-sm: clamp(0.5rem, 0.4rem + 0.5vw, 1rem);
    --space-md: clamp(1rem, 0.8rem + 1vw, 1.5rem);
    --space-lg: clamp(1.5rem, 1.2rem + 1.5vw, 2rem);
    --space-xl: clamp(2rem, 1.6rem + 2vw, 3rem);
    --space-2xl: clamp(3rem, 2.4rem + 3vw, 4rem);
    --space-3xl: clamp(4rem, 3.2rem + 4vw, 6rem);
}

/* Spacing utilities */
.p-xs { padding: var(--space-xs); }
.p-sm { padding: var(--space-sm); }
.p-md { padding: var(--space-md); }
.p-lg { padding: var(--space-lg); }
.p-xl { padding: var(--space-xl); }

.m-xs { margin: var(--space-xs); }
.m-sm { margin: var(--space-sm); }
.m-md { margin: var(--space-md); }
.m-lg { margin: var(--space-lg); }
.m-xl { margin: var(--space-xl); }

/* Responsive gaps */
.gap-xs { gap: var(--space-xs); }
.gap-sm { gap: var(--space-sm); }
.gap-md { gap: var(--space-md); }
.gap-lg { gap: var(--space-lg); }
.gap-xl { gap: var(--space-xl); }
```

## Exercise: Responsive Design System

Create a responsive design system with:
- Fluid typography using clamp() functions
- Responsive spacing with CSS custom properties
- Container queries for component-level responsiveness
- Viewport units for full-screen layouts
- Calculation functions for complex layouts
- Mobile-first approach throughout

## Key Takeaways
- Use viewport units (vw, vh, vmin, vmax) for viewport-relative sizing
- Prefer rem units for consistent scaling across components
- Use em units when you need relative sizing to parent elements
- Leverage calc() for complex calculations and responsive layouts
- Use min(), max(), and clamp() for constrained responsive values
- Implement container queries for component-level responsiveness
- Create fluid typography and spacing systems
- Test responsive units across different devices and screen sizes
- Combine different unit types for optimal responsive behavior
- Use CSS custom properties for maintainable responsive systems
