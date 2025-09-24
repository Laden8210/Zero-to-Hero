# Layout Systems Mastery

## Introduction
Modern CSS provides powerful layout systems: Flexbox for one-dimensional layouts and CSS Grid for two-dimensional layouts. Mastering these tools is essential for creating complex, responsive designs.

## Flexbox Comprehensive Understanding

### Flexbox Container Properties
```css
/* Basic flexbox setup */
.flex-container {
    display: flex;
    /* Creates flexbox layout context */
}

/* Flex direction - controls main axis */
.flex-row {
    flex-direction: row; /* Default: left to right */
}

.flex-row-reverse {
    flex-direction: row-reverse; /* Right to left */
}

.flex-column {
    flex-direction: column; /* Top to bottom */
}

.flex-column-reverse {
    flex-direction: column-reverse; /* Bottom to top */
}

/* Flex wrap - controls wrapping behavior */
.flex-wrap {
    flex-wrap: wrap; /* Items wrap to new line */
}

.flex-nowrap {
    flex-wrap: nowrap; /* Default: single line */
}

.flex-wrap-reverse {
    flex-wrap: wrap-reverse; /* Wrap in reverse order */
}
```

**Code Explanation:**
- `display: flex`: Enables flexbox layout for the container and its direct children
- `flex-direction: row`: Sets main axis horizontally (left to right) - default behavior
- `flex-direction: row-reverse`: Reverses main axis direction (right to left)
- `flex-direction: column`: Sets main axis vertically (top to bottom)
- `flex-direction: column-reverse`: Reverses vertical main axis (bottom to top)
- `flex-wrap: wrap`: Allows flex items to wrap to new lines when container width is exceeded
- `flex-wrap: nowrap`: Forces all items to stay on single line (default)
- `flex-wrap: wrap-reverse`: Wraps items but in reverse order

**Benefits:**
- Flexbox provides powerful one-dimensional layout control
- Direction properties control how items flow in the container
- Wrap properties handle responsive behavior automatically
- Easy to create complex layouts with minimal code
- Works well for navigation bars, card layouts, and form controls

### Main Axis Alignment (justify-content)
```css
.justify-start {
    justify-content: flex-start; /* Default: start of main axis */
}

.justify-end {
    justify-content: flex-end; /* End of main axis */
}

.justify-center {
    justify-content: center; /* Center of main axis */
}

.justify-between {
    justify-content: space-between; /* Equal space between items */
}

.justify-around {
    justify-content: space-around; /* Equal space around items */
}

.justify-evenly {
    justify-content: space-evenly; /* Equal space everywhere */
}
```

**Code Explanation:**
- `justify-content: flex-start`: Aligns items to the start of the main axis (left for row, top for column)
- `justify-content: flex-end`: Aligns items to the end of the main axis (right for row, bottom for column)
- `justify-content: center`: Centers items along the main axis
- `justify-content: space-between`: Distributes items with equal space between them (no space at edges)
- `justify-content: space-around`: Distributes items with equal space around each item
- `justify-content: space-evenly`: Distributes items with equal space everywhere (including edges)

**Benefits:**
- Provides precise control over horizontal spacing in row layouts
- Centers content easily without margin calculations
- Creates equal spacing between multiple items
- Works responsively across different screen sizes
- Essential for navigation bars, button groups, and card layouts

### Cross Axis Alignment (align-items)
```css
.align-stretch {
    align-items: stretch; /* Default: stretch to fill container */
}

.align-start {
    align-items: flex-start; /* Start of cross axis */
}

.align-end {
    align-items: flex-end; /* End of cross axis */
}

.align-center {
    align-items: center; /* Center of cross axis */
}

.align-baseline {
    align-items: baseline; /* Align text baselines */
}
```

**Code Explanation:**
- `align-items: stretch`: Stretches items to fill the container height (default behavior)
- `align-items: flex-start`: Aligns items to the start of the cross axis (top for row, left for column)
- `align-items: flex-end`: Aligns items to the end of the cross axis (bottom for row, right for column)
- `align-items: center`: Centers items along the cross axis
- `align-items: baseline`: Aligns items by their text baseline (useful for text-heavy layouts)

**Benefits:**
- Controls vertical alignment in row layouts
- Centers content vertically without complex positioning
- Baseline alignment ensures text appears aligned across items
- Essential for creating balanced layouts with different-sized content
- Works perfectly with justify-content for complete alignment control

### Flex Item Properties
```css
.flex-item {
    /* Individual item properties */
}

/* Flex grow - how much item grows */
.flex-grow-1 {
    flex-grow: 1; /* Grow to fill available space */
}

.flex-grow-2 {
    flex-grow: 2; /* Grow twice as much as flex-grow: 1 */
}

/* Flex shrink - how much item shrinks */
.flex-shrink-0 {
    flex-shrink: 0; /* Don't shrink */
}

.flex-shrink-1 {
    flex-shrink: 1; /* Default: shrink proportionally */
}

/* Flex basis - initial size before growing/shrinking */
.flex-basis-200 {
    flex-basis: 200px; /* Initial size of 200px */
}

.flex-basis-auto {
    flex-basis: auto; /* Default: based on content */
}

/* Shorthand flex property */
.flex-shorthand {
    flex: 1 1 200px; /* grow shrink basis */
}

.flex-auto {
    flex: 1 1 auto; /* Common pattern */
}

.flex-none {
    flex: 0 0 auto; /* Don't grow or shrink */
}
```

### Practical Flexbox Examples

#### Navigation Bar
```css
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
    background: #2c3e50;
    color: white;
}

.navbar-brand {
    font-size: 1.5rem;
    font-weight: bold;
}

.navbar-nav {
    display: flex;
    list-style: none;
    margin: 0;
    padding: 0;
    gap: 2rem;
}

.navbar-nav a {
    color: white;
    text-decoration: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    transition: background-color 0.3s;
}

.navbar-nav a:hover {
    background-color: rgba(255, 255, 255, 0.1);
}
```

#### Card Layout
```css
.card-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    padding: 1rem;
}

.card {
    flex: 1 1 300px; /* Grow, shrink, basis */
    min-width: 300px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    overflow: hidden;
}

.card-header {
    padding: 1rem;
    background: #f8f9fa;
    border-bottom: 1px solid #dee2e6;
}

.card-body {
    padding: 1rem;
}

.card-footer {
    padding: 1rem;
    background: #f8f9fa;
    border-top: 1px solid #dee2e6;
}
```

#### Centering Content
```css
.center-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: #f8f9fa;
}

.centered-content {
    text-align: center;
    padding: 2rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
```

## CSS Grid Advanced Techniques

### Grid Container Properties
```css
.grid-container {
    display: grid;
    /* Creates grid layout context */
}

/* Grid template columns */
.grid-cols-3 {
    grid-template-columns: 1fr 1fr 1fr; /* Three equal columns */
}

.grid-cols-auto {
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    /* Responsive columns */
}

.grid-cols-named {
    grid-template-columns: [sidebar-start] 250px [sidebar-end main-start] 1fr [main-end];
    /* Named grid lines */
}

/* Grid template rows */
.grid-rows-3 {
    grid-template-rows: auto 1fr auto; /* Header, content, footer */
}

/* Grid template areas */
.grid-areas {
    grid-template-areas: 
        "header header header"
        "sidebar main main"
        "footer footer footer";
}

/* Grid gap */
.grid-gap {
    gap: 1rem; /* Shorthand for row-gap and column-gap */
}

.grid-gap-separate {
    row-gap: 1rem;
    column-gap: 2rem;
}
```

### Grid Item Properties
```css
.grid-item {
    /* Individual item properties */
}

/* Grid column positioning */
.col-span-2 {
    grid-column: span 2; /* Span 2 columns */
}

.col-start-2 {
    grid-column-start: 2; /* Start at column 2 */
}

.col-end-4 {
    grid-column-end: 4; /* End at column 4 */
}

.col-2-4 {
    grid-column: 2 / 4; /* From column 2 to 4 */
}

/* Grid row positioning */
.row-span-2 {
    grid-row: span 2; /* Span 2 rows */
}

.row-start-2 {
    grid-row-start: 2; /* Start at row 2 */
}

.row-end-4 {
    grid-row-end: 4; /* End at row 4 */
}

.row-2-4 {
    grid-row: 2 / 4; /* From row 2 to 4 */
}

/* Grid area */
.grid-area {
    grid-area: main; /* Place in named grid area */
}

.grid-area-shorthand {
    grid-area: 1 / 2 / 3 / 4; /* row-start / col-start / row-end / col-end */
}
```

### Advanced Grid Features
```css
/* Minmax function */
.responsive-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1rem;
}

/* Auto-fill vs auto-fit */
.auto-fill {
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    /* Creates empty columns if needed */
}

.auto-fit {
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    /* Collapses empty columns */
}

/* Subgrid (modern browsers) */
.subgrid-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
}

.subgrid-item {
    display: grid;
    grid-template-columns: subgrid;
    grid-column: 1 / -1;
}
```

### Practical Grid Examples

#### Holy Grail Layout
```css
.holy-grail {
    display: grid;
    grid-template-areas: 
        "header header header"
        "nav main aside"
        "footer footer footer";
    grid-template-columns: 200px 1fr 200px;
    grid-template-rows: auto 1fr auto;
    min-height: 100vh;
}

.holy-grail-header {
    grid-area: header;
    background: #2c3e50;
    color: white;
    padding: 1rem;
}

.holy-grail-nav {
    grid-area: nav;
    background: #34495e;
    color: white;
    padding: 1rem;
}

.holy-grail-main {
    grid-area: main;
    background: white;
    padding: 1rem;
}

.holy-grail-aside {
    grid-area: aside;
    background: #ecf0f1;
    padding: 1rem;
}

.holy-grail-footer {
    grid-area: footer;
    background: #2c3e50;
    color: white;
    padding: 1rem;
}
```

#### Magazine Layout
```css
.magazine-layout {
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    grid-template-rows: repeat(8, 100px);
    gap: 1rem;
    padding: 1rem;
}

.featured-article {
    grid-column: 1 / 8;
    grid-row: 1 / 5;
    background: #3498db;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}

.sidebar-article {
    grid-column: 8 / 13;
    grid-row: 1 / 3;
    background: #e74c3c;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}

.small-article-1 {
    grid-column: 8 / 13;
    grid-row: 3 / 5;
    background: #27ae60;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}

.small-article-2 {
    grid-column: 1 / 4;
    grid-row: 5 / 7;
    background: #f39c12;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}

.small-article-3 {
    grid-column: 4 / 7;
    grid-row: 5 / 7;
    background: #9b59b6;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}

.small-article-4 {
    grid-column: 7 / 13;
    grid-row: 5 / 7;
    background: #1abc9c;
    color: white;
    padding: 1rem;
    border-radius: 8px;
}
```

#### Responsive Grid System
```css
.responsive-grid-system {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1rem;
    padding: 1rem;
}

@media (min-width: 768px) {
    .responsive-grid-system {
        grid-template-columns: repeat(2, 1fr);
    }
}

@media (min-width: 1024px) {
    .responsive-grid-system {
        grid-template-columns: repeat(3, 1fr);
    }
}

@media (min-width: 1200px) {
    .responsive-grid-system {
        grid-template-columns: repeat(4, 1fr);
    }
}
```

## Exercise: Create a Dashboard Layout

Create a responsive dashboard layout using both Flexbox and CSS Grid:
- Use CSS Grid for the main layout structure
- Use Flexbox for component internal layouts
- Implement responsive breakpoints
- Include a sidebar that collapses on mobile
- Add a header with navigation
- Create a main content area with cards

## Key Takeaways
- Flexbox is perfect for one-dimensional layouts
- CSS Grid excels at two-dimensional layouts
- Use both together for complex layouts
- Grid areas make layouts more semantic
- Auto-fit and auto-fill create responsive grids
- Practice with real-world layout patterns
