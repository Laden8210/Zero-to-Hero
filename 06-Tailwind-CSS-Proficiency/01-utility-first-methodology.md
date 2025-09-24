# Utility-First Methodology

## Introduction
Tailwind CSS is a utility-first CSS framework that provides low-level utility classes to build custom designs. This lesson covers the philosophy, benefits, and core utility categories of Tailwind CSS.

## Philosophy and Benefits

### Utility-First Approach
```html
<!-- Traditional CSS approach -->
<div class="card">
    <h2 class="card-title">Card Title</h2>
    <p class="card-content">Card content goes here...</p>
</div>

<style>
.card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    margin: 1rem;
}

.card-title {
    font-size: 1.25rem;
    font-weight: bold;
    margin-bottom: 0.5rem;
    color: #1f2937;
}

.card-content {
    color: #6b7280;
    line-height: 1.5;
}
</style>

<!-- Tailwind utility-first approach -->
<div class="bg-white rounded-lg shadow-md p-4 m-4">
    <h2 class="text-xl font-bold mb-2 text-gray-800">Card Title</h2>
    <p class="text-gray-600 leading-relaxed">Card content goes here...</p>
</div>
```

**Code Explanation:**
- **Traditional approach**: Uses semantic class names (`.card`, `.card-title`) with custom CSS
- **Tailwind approach**: Uses utility classes directly in HTML for styling
- `bg-white`: Sets background color to white
- `rounded-lg`: Applies large border radius (8px)
- `shadow-md`: Applies medium drop shadow
- `p-4`: Sets padding to 1rem (16px) on all sides
- `m-4`: Sets margin to 1rem (16px) on all sides
- `text-xl`: Sets font size to 1.25rem (20px)
- `font-bold`: Sets font weight to bold (700)
- `mb-2`: Sets margin bottom to 0.5rem (8px)
- `text-gray-800`: Sets text color to dark gray
- `text-gray-600`: Sets text color to medium gray
- `leading-relaxed`: Sets line height to 1.625

**Benefits:**
- Faster development with no context switching between HTML and CSS
- Consistent design system with predefined values
- Smaller CSS bundle size through purging unused styles
- Easy to maintain and modify styles directly in HTML
- Better responsive design with built-in breakpoint prefixes

### Benefits of Utility-First
```html
<!-- Rapid prototyping -->
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
    Click me
</button>

<!-- Consistent design system -->
<div class="space-y-4">
    <h1 class="text-3xl font-bold text-gray-900">Heading 1</h1>
    <h2 class="text-2xl font-semibold text-gray-800">Heading 2</h2>
    <h3 class="text-xl font-medium text-gray-700">Heading 3</h3>
</div>

<!-- No context switching -->
<div class="flex items-center justify-between p-6 bg-white rounded-lg shadow">
    <div class="flex items-center space-x-3">
        <img class="w-10 h-10 rounded-full" src="avatar.jpg" alt="Avatar">
        <div>
            <p class="text-sm font-medium text-gray-900">John Doe</p>
            <p class="text-sm text-gray-500">john@example.com</p>
        </div>
    </div>
    <button class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600">
        Follow
    </button>
</div>
```

**Code Explanation:**
- `bg-blue-500`: Sets background color to blue-500 (medium blue)
- `hover:bg-blue-700`: Changes background to darker blue on hover
- `text-white`: Sets text color to white
- `font-bold`: Sets font weight to bold
- `py-2 px-4`: Sets vertical padding to 0.5rem and horizontal padding to 1rem
- `rounded`: Applies small border radius (4px)
- `space-y-4`: Adds vertical spacing of 1rem between child elements
- `text-3xl`: Sets font size to 1.875rem (30px)
- `text-2xl`: Sets font size to 1.5rem (24px)
- `text-xl`: Sets font size to 1.25rem (20px)
- `font-semibold`: Sets font weight to 600
- `font-medium`: Sets font weight to 500
- `flex items-center justify-between`: Creates flexbox with center alignment and space between
- `space-x-3`: Adds horizontal spacing of 0.75rem between child elements
- `w-10 h-10`: Sets width and height to 2.5rem (40px)
- `rounded-full`: Makes element perfectly circular
- `text-sm`: Sets font size to 0.875rem (14px)

**Benefits:**
- Rapid prototyping without writing custom CSS
- Consistent spacing and sizing using predefined scale
- Hover states and interactions built-in
- Responsive design with mobile-first approach
- Easy to copy and reuse component styles

## Core Utility Categories

### Layout Utilities
```html
<!-- Display utilities -->
<div class="block">Block element</div>
<div class="inline">Inline element</div>
<div class="inline-block">Inline-block element</div>
<div class="flex">Flex container</div>
<div class="grid">Grid container</div>
<div class="hidden">Hidden element</div>

<!-- Flexbox utilities -->
<div class="flex flex-col md:flex-row items-center justify-between">
    <div class="flex-1">Flex item 1</div>
    <div class="flex-1">Flex item 2</div>
</div>

<!-- Grid utilities -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <div class="bg-gray-100 p-4">Grid item 1</div>
    <div class="bg-gray-100 p-4">Grid item 2</div>
    <div class="bg-gray-100 p-4">Grid item 3</div>
</div>

<!-- Positioning -->
<div class="relative">
    <div class="absolute top-0 right-0 bg-red-500 text-white p-2">
        Absolute positioned
    </div>
    <div class="sticky top-0 bg-blue-500 text-white p-2">
        Sticky positioned
    </div>
</div>
```

### Spacing Utilities
```html
<!-- Margin utilities -->
<div class="m-4">Margin all sides</div>
<div class="mx-auto my-4">Horizontal center, vertical margin</div>
<div class="mt-8 mb-4 ml-2 mr-6">Individual margins</div>

<!-- Padding utilities -->
<div class="p-6">Padding all sides</div>
<div class="px-4 py-2">Horizontal and vertical padding</div>
<div class="pt-4 pb-2 pl-6 pr-8">Individual padding</div>

<!-- Gap utilities -->
<div class="flex gap-4">
    <div>Item 1</div>
    <div>Item 2</div>
    <div>Item 3</div>
</div>

<div class="grid grid-cols-3 gap-6">
    <div>Grid item 1</div>
    <div>Grid item 2</div>
    <div>Grid item 3</div>
</div>

<!-- Space between utilities -->
<div class="flex justify-between">
    <div>Left item</div>
    <div>Right item</div>
</div>

<div class="space-y-4">
    <div>Item 1</div>
    <div>Item 2</div>
    <div>Item 3</div>
</div>
```

### Sizing Utilities
```html
<!-- Width utilities -->
<div class="w-full">Full width</div>
<div class="w-1/2">Half width</div>
<div class="w-1/3">Third width</div>
<div class="w-1/4">Quarter width</div>
<div class="w-64">Fixed width (16rem)</div>
<div class="w-auto">Auto width</div>

<!-- Height utilities -->
<div class="h-screen">Full screen height</div>
<div class="h-64">Fixed height (16rem)</div>
<div class="h-auto">Auto height</div>

<!-- Min/Max dimensions -->
<div class="min-h-screen">Minimum full height</div>
<div class="max-w-md">Maximum medium width</div>
<div class="min-w-0">Minimum zero width</div>

<!-- Responsive sizing -->
<div class="w-full md:w-1/2 lg:w-1/3">
    Responsive width
</div>
```

### Typography Utilities
```html
<!-- Font family -->
<h1 class="font-sans">Sans-serif font</h1>
<h1 class="font-serif">Serif font</h1>
<h1 class="font-mono">Monospace font</h1>

<!-- Font size -->
<h1 class="text-xs">Extra small text</h1>
<h1 class="text-sm">Small text</h1>
<h1 class="text-base">Base text</h1>
<h1 class="text-lg">Large text</h1>
<h1 class="text-xl">Extra large text</h1>
<h1 class="text-2xl">2x large text</h1>
<h1 class="text-3xl">3x large text</h1>

<!-- Font weight -->
<p class="font-thin">Thin weight</p>
<p class="font-light">Light weight</p>
<p class="font-normal">Normal weight</p>
<p class="font-medium">Medium weight</p>
<p class="font-semibold">Semi-bold weight</p>
<p class="font-bold">Bold weight</p>

<!-- Text alignment -->
<p class="text-left">Left aligned</p>
<p class="text-center">Center aligned</p>
<p class="text-right">Right aligned</p>
<p class="text-justify">Justified text</p>

<!-- Line height -->
<p class="leading-tight">Tight line height</p>
<p class="leading-normal">Normal line height</p>
<p class="leading-relaxed">Relaxed line height</p>
<p class="leading-loose">Loose line height</p>

<!-- Letter spacing -->
<p class="tracking-tight">Tight letter spacing</p>
<p class="tracking-normal">Normal letter spacing</p>
<p class="tracking-wide">Wide letter spacing</p>
```

### Color Utilities
```html
<!-- Text colors -->
<p class="text-gray-900">Dark gray text</p>
<p class="text-gray-600">Medium gray text</p>
<p class="text-gray-400">Light gray text</p>
<p class="text-blue-600">Blue text</p>
<p class="text-red-500">Red text</p>
<p class="text-green-600">Green text</p>

<!-- Background colors -->
<div class="bg-white">White background</div>
<div class="bg-gray-100">Light gray background</div>
<div class="bg-blue-500">Blue background</div>
<div class="bg-red-500">Red background</div>
<div class="bg-green-500">Green background</div>

<!-- Border colors -->
<div class="border border-gray-300">Gray border</div>
<div class="border-2 border-blue-500">Blue border</div>
<div class="border-l-4 border-red-500">Red left border</div>

<!-- Hover states -->
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
    Hover me
</button>

<!-- Focus states -->
<input class="border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-200 rounded px-3 py-2">
```

### Border Utilities
```html
<!-- Border width -->
<div class="border">Default border</div>
<div class="border-2">2px border</div>
<div class="border-4">4px border</div>
<div class="border-t-2">Top border only</div>
<div class="border-b-4">Bottom border only</div>

<!-- Border radius -->
<div class="rounded">Small radius</div>
<div class="rounded-md">Medium radius</div>
<div class="rounded-lg">Large radius</div>
<div class="rounded-xl">Extra large radius</div>
<div class="rounded-full">Fully rounded</div>

<!-- Border styles -->
<div class="border-solid">Solid border</div>
<div class="border-dashed">Dashed border</div>
<div class="border-dotted">Dotted border</div>
<div class="border-none">No border</div>
```

### Effects Utilities
```html
<!-- Box shadow -->
<div class="shadow-sm">Small shadow</div>
<div class="shadow">Default shadow</div>
<div class="shadow-md">Medium shadow</div>
<div class="shadow-lg">Large shadow</div>
<div class="shadow-xl">Extra large shadow</div>
<div class="shadow-2xl">2x large shadow</div>

<!-- Opacity -->
<div class="opacity-100">Fully opaque</div>
<div class="opacity-75">75% opacity</div>
<div class="opacity-50">50% opacity</div>
<div class="opacity-25">25% opacity</div>
<div class="opacity-0">Fully transparent</div>

<!-- Mix blend modes -->
<div class="mix-blend-multiply">Multiply blend</div>
<div class="mix-blend-screen">Screen blend</div>
<div class="mix-blend-overlay">Overlay blend</div>
```

## Practical Examples

### Card Component
```html
<div class="max-w-sm mx-auto bg-white rounded-xl shadow-lg overflow-hidden">
    <img class="w-full h-48 object-cover" src="image.jpg" alt="Card image">
    <div class="p-6">
        <h2 class="text-xl font-semibold text-gray-900 mb-2">Card Title</h2>
        <p class="text-gray-600 text-sm leading-relaxed mb-4">
            This is a description of the card content. It can be multiple lines long.
        </p>
        <div class="flex items-center justify-between">
            <span class="text-sm text-gray-500">Category</span>
            <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
                Read More
            </button>
        </div>
    </div>
</div>
```

### Navigation Bar
```html
<nav class="bg-white shadow-lg">
    <div class="max-w-7xl mx-auto px-4">
        <div class="flex justify-between h-16">
            <div class="flex items-center">
                <div class="flex-shrink-0">
                    <h1 class="text-xl font-bold text-gray-800">Logo</h1>
                </div>
                <div class="hidden md:block">
                    <div class="ml-10 flex items-baseline space-x-4">
                        <a href="#" class="text-gray-900 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">Home</a>
                        <a href="#" class="text-gray-600 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">About</a>
                        <a href="#" class="text-gray-600 hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium">Contact</a>
                    </div>
                </div>
            </div>
            <div class="flex items-center">
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
                    Sign In
                </button>
            </div>
        </div>
    </div>
</nav>
```

### Form Elements
```html
<form class="max-w-md mx-auto bg-white p-6 rounded-lg shadow-md">
    <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
            Email Address
        </label>
        <input 
            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:border-blue-500" 
            id="email" 
            type="email" 
            placeholder="Enter your email"
        >
    </div>
    
    <div class="mb-6">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="password">
            Password
        </label>
        <input 
            class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline focus:border-blue-500" 
            id="password" 
            type="password" 
            placeholder="Enter your password"
        >
    </div>
    
    <div class="flex items-center justify-between">
        <button 
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" 
            type="button"
        >
            Sign In
        </button>
        <a class="inline-block align-baseline font-bold text-sm text-blue-500 hover:text-blue-800" href="#">
            Forgot Password?
        </a>
    </div>
</form>
```

## Exercise: Utility-First Component Library

Create a component library using only Tailwind utilities:
- Button component with multiple variants
- Card component with different styles
- Form components with validation states
- Navigation component with responsive design
- Modal component with overlay and animations

## Key Takeaways
- Utility-first approach provides rapid development and consistency
- No context switching between HTML and CSS files
- Small bundle sizes through PurgeCSS optimization
- Consistent design system enforcement
- Easy to maintain and modify designs
- Perfect for prototyping and production applications
- Focus on composition rather than custom CSS
