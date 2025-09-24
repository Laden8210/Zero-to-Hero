# Advanced CSS Features

## Introduction
Modern CSS offers powerful features for creating sophisticated user interfaces. This lesson covers transitions, animations, transforms, filters, and other advanced CSS capabilities.

## Transitions and Animations

### CSS Transitions
```css
/* Basic transition properties */
.button {
    background-color: #007bff;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    
    /* Transition properties */
    transition-property: background-color, transform, box-shadow;
    transition-duration: 0.3s;
    transition-timing-function: ease-in-out;
    transition-delay: 0s;
    
    /* Shorthand */
    transition: all 0.3s ease-in-out;
}

.button:hover {
    background-color: #0056b3;
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

/* Multiple transitions with different timings */
.card {
    background: white;
    border-radius: 8px;
    padding: 1rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    
    transition: 
        transform 0.2s ease-out,
        box-shadow 0.3s ease-out,
        background-color 0.1s ease-out;
}

.card:hover {
    transform: translateY(-4px);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
    background-color: #f8f9fa;
}

/* Transition with different timing functions */
.element {
    width: 100px;
    height: 100px;
    background: #007bff;
    
    transition: 
        width 1s ease-in,
        height 1s ease-out,
        background-color 1s linear,
        transform 1s ease-in-out;
}

.element:hover {
    width: 200px;
    height: 200px;
    background-color: #28a745;
    transform: rotate(45deg);
}
```

### CSS Animations
```css
/* Keyframe animations */
@keyframes slideIn {
    from {
        transform: translateX(-100%);
        opacity: 0;
    }
    to {
        transform: translateX(0);
        opacity: 1;
    }
}

@keyframes bounce {
    0%, 20%, 53%, 80%, 100% {
        transform: translate3d(0, 0, 0);
    }
    40%, 43% {
        transform: translate3d(0, -30px, 0);
    }
    70% {
        transform: translate3d(0, -15px, 0);
    }
    90% {
        transform: translate3d(0, -4px, 0);
    }
}

@keyframes pulse {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.05);
    }
    100% {
        transform: scale(1);
    }
}

@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translate3d(0, 40px, 0);
    }
    to {
        opacity: 1;
        transform: translate3d(0, 0, 0);
    }
}

/* Animation properties */
.animated-element {
    animation-name: slideIn;
    animation-duration: 0.5s;
    animation-timing-function: ease-out;
    animation-delay: 0.2s;
    animation-iteration-count: 1;
    animation-direction: normal;
    animation-fill-mode: both;
    animation-play-state: running;
    
    /* Shorthand */
    animation: slideIn 0.5s ease-out 0.2s 1 normal both;
}

/* Hover animations */
.hover-bounce:hover {
    animation: bounce 1s infinite;
}

.hover-pulse:hover {
    animation: pulse 2s infinite;
}

/* Loading animations */
.loading-spinner {
    width: 40px;
    height: 40px;
    border: 4px solid #f3f3f3;
    border-top: 4px solid #007bff;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
```

## Transform and 3D Effects

### 2D Transforms
```css
/* Basic 2D transforms */
.transform-demo {
    width: 100px;
    height: 100px;
    background: #007bff;
    margin: 20px;
    transition: transform 0.3s ease;
}

/* Translate */
.translate-x:hover {
    transform: translateX(20px);
}

.translate-y:hover {
    transform: translateY(-20px);
}

.translate-xy:hover {
    transform: translate(20px, -20px);
}

/* Rotate */
.rotate:hover {
    transform: rotate(45deg);
}

/* Scale */
.scale:hover {
    transform: scale(1.2);
}

.scale-x:hover {
    transform: scaleX(1.5);
}

.scale-y:hover {
    transform: scaleY(0.8);
}

/* Skew */
.skew:hover {
    transform: skew(15deg, 5deg);
}

/* Multiple transforms */
.complex-transform:hover {
    transform: translate(20px, -20px) rotate(45deg) scale(1.2);
}

/* Transform origin */
.transform-origin {
    transform-origin: center center;
}

.transform-origin-top-left:hover {
    transform: rotate(45deg);
    transform-origin: top left;
}
```

### 3D Transforms
```css
/* 3D transform container */
.transform-3d-container {
    perspective: 1000px;
    perspective-origin: center center;
}

/* 3D transforms */
.card-3d {
    width: 200px;
    height: 200px;
    background: linear-gradient(45deg, #007bff, #28a745);
    margin: 50px auto;
    transition: transform 0.6s ease;
    transform-style: preserve-3d;
}

.card-3d:hover {
    transform: rotateY(180deg);
}

/* Card flip effect */
.flip-card {
    width: 300px;
    height: 200px;
    perspective: 1000px;
}

.flip-card-inner {
    position: relative;
    width: 100%;
    height: 100%;
    text-align: center;
    transition: transform 0.6s;
    transform-style: preserve-3d;
}

.flip-card:hover .flip-card-inner {
    transform: rotateY(180deg);
}

.flip-card-front,
.flip-card-back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-size: 1.2rem;
}

.flip-card-front {
    background: #007bff;
}

.flip-card-back {
    background: #28a745;
    transform: rotateY(180deg);
}

/* 3D cube */
.cube-container {
    perspective: 1000px;
    perspective-origin: center center;
}

.cube {
    width: 100px;
    height: 100px;
    position: relative;
    transform-style: preserve-3d;
    animation: rotateCube 6s infinite linear;
}

.cube-face {
    position: absolute;
    width: 100px;
    height: 100px;
    background: rgba(0, 123, 255, 0.8);
    border: 2px solid #007bff;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-weight: bold;
}

.cube-face:nth-child(1) { transform: rotateY(0deg) translateZ(50px); }
.cube-face:nth-child(2) { transform: rotateY(90deg) translateZ(50px); }
.cube-face:nth-child(3) { transform: rotateY(180deg) translateZ(50px); }
.cube-face:nth-child(4) { transform: rotateY(-90deg) translateZ(50px); }
.cube-face:nth-child(5) { transform: rotateX(90deg) translateZ(50px); }
.cube-face:nth-child(6) { transform: rotateX(-90deg) translateZ(50px); }

@keyframes rotateCube {
    0% { transform: rotateX(0deg) rotateY(0deg); }
    100% { transform: rotateX(360deg) rotateY(360deg); }
}
```

## CSS Filters and Effects

### Filter Effects
```css
/* Basic filter effects */
.filter-demo {
    width: 200px;
    height: 200px;
    background: url('image.jpg') center/cover;
    margin: 20px;
    transition: filter 0.3s ease;
}

/* Blur */
.blur:hover {
    filter: blur(5px);
}

/* Brightness */
.brightness:hover {
    filter: brightness(1.5);
}

/* Contrast */
.contrast:hover {
    filter: contrast(1.5);
}

/* Grayscale */
.grayscale:hover {
    filter: grayscale(100%);
}

/* Hue rotate */
.hue-rotate:hover {
    filter: hue-rotate(90deg);
}

/* Invert */
.invert:hover {
    filter: invert(100%);
}

/* Opacity */
.opacity:hover {
    filter: opacity(0.5);
}

/* Saturate */
.saturate:hover {
    filter: saturate(2);
}

/* Sepia */
.sepia:hover {
    filter: sepia(100%);
}

/* Multiple filters */
.multiple-filters:hover {
    filter: blur(2px) brightness(1.2) contrast(1.1) saturate(1.3);
}
```

### Backdrop Filters
```css
/* Backdrop filter for glassmorphism effect */
.glassmorphism {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 12px;
    padding: 2rem;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

/* Modal overlay with backdrop filter */
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.3);
    backdrop-filter: blur(5px);
    display: flex;
    align-items: center;
    justify-content: center;
}

.modal-content {
    background: white;
    border-radius: 8px;
    padding: 2rem;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
}
```

## CSS Custom Properties (Variables)

### Dynamic Theming
```css
/* CSS custom properties for theming */
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

/* Dark theme */
[data-theme="dark"] {
    --primary-color: #0d6efd;
    --secondary-color: #6c757d;
    --success-color: #198754;
    --danger-color: #dc3545;
    --warning-color: #fd7e14;
    --info-color: #0dcaf0;
    
    --bg-color: #212529;
    --text-color: #ffffff;
    --border-color: #495057;
}

/* Component using CSS variables */
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
    transform: translateY(-1px);
}

/* Dynamic color generation */
.color-palette {
    --base-hue: 200;
    --color-1: hsl(var(--base-hue), 70%, 50%);
    --color-2: hsl(calc(var(--base-hue) + 60), 70%, 50%);
    --color-3: hsl(calc(var(--base-hue) + 120), 70%, 50%);
    --color-4: hsl(calc(var(--base-hue) + 180), 70%, 50%);
    --color-5: hsl(calc(var(--base-hue) + 240), 70%, 50%);
    --color-6: hsl(calc(var(--base-hue) + 300), 70%, 50%);
}

.palette-item {
    width: 100px;
    height: 100px;
    border-radius: var(--border-radius);
}

.palette-item:nth-child(1) { background-color: var(--color-1); }
.palette-item:nth-child(2) { background-color: var(--color-2); }
.palette-item:nth-child(3) { background-color: var(--color-3); }
.palette-item:nth-child(4) { background-color: var(--color-4); }
.palette-item:nth-child(5) { background-color: var(--color-5); }
.palette-item:nth-child(6) { background-color: var(--color-6); }
```

## Exercise: Advanced CSS Features Showcase

Create a showcase of advanced CSS features:
- Interactive animations and transitions
- 3D transform effects and card flips
- Filter effects and glassmorphism
- Dynamic theming with CSS variables
- Responsive animations that adapt to screen size
- Performance-optimized animations

## Key Takeaways
- Transitions provide smooth property changes
- Animations create complex, repeating effects
- Transforms enable 2D and 3D transformations
- Filters add visual effects to elements
- Backdrop filters create glassmorphism effects
- CSS custom properties enable dynamic theming
- Use transform and opacity for performant animations
- Consider reduced motion preferences
- Test animations on different devices
- Use will-change property for optimization
